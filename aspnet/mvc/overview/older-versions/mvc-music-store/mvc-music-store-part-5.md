---
uid: mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-5
title: "Partie 5 : Modification de formulaires et création de modèles | Documents Microsoft"
author: jongalloway
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 5 couvre la modification de formulaires et de création de modèles."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/21/2011
ms.topic: article
ms.assetid: 6b09413a-6d6a-425a-87c9-629f91b91b28
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-5
msc.type: authoredcontent
ms.openlocfilehash: cde6fe133291254531a797a434a4b2cdd226dd5f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-5-edit-forms-and-templating"></a>Partie 5 : Modification de formulaires et création de modèles
====================
par [Jon Galloway](https://github.com/jongalloway)

> Le magasin de musique MVC est une application du didacticiel qui présente et explique étape par étape comment utiliser ASP.NET MVC et Visual Studio pour le développement web.  
>   
> Le magasin de musique MVC est une implémentation de magasin exemple léger qui vend des albums de musique en ligne et implémente l’administration de site de base, authentification de l’utilisateur et les fonctionnalités de panier d’achat.
> 
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 5 couvre la modification de formulaires et de création de modèles.


Dans le chapitre passé, nous avons le chargement de données à partir de notre base de données et afficher. Dans ce chapitre, nous allons également activer la modification des données.

## <a name="creating-the-storemanagercontroller"></a>Création de la StoreManagerController

Nous allons commencer par créer un nouveau contrôleur appelé **StoreManagerController**. Pour ce contrôleur, nous allons parti des fonctionnalités de génération de modèles automatique est disponibles dans ASP.NET MVC 3 Tools Update. Définissez les options de la boîte de dialogue Ajouter un contrôleur, comme indiqué ci-dessous.

![](mvc-music-store-part-5/_static/image1.png)

Lorsque vous cliquez sur le bouton Ajouter, vous verrez que le mécanisme de génération de modèles automatique ASP.NET MVC 3 effectue une quantité importante de travail pour vous :

- Il crée la nouvelle StoreManagerController avec une variable locale de l’Entity Framework
- Il ajoute un dossier StoreManager vers le dossier du projet vues
- Il ajoute une vue Create.cshtml, Delete.cshtml, Details.cshtml, Edit.cshtml et Index.cshtml, fortement typée de la classe Album

![](mvc-music-store-part-5/_static/image2.png)

La nouvelle classe de contrôleur StoreManager inclut CRUD (créer, lire, mettre à jour, supprimer) des actions de contrôleur qui savent comment travailler avec l’Album classe de modèle et utiliser notre contexte Entity Framework pour l’accès de la base de données.

## <a name="modifying-a-scaffolded-view"></a>Modification d’une vue de modèle généré automatiquement

Il est important de se rappeler que, même si ce code a été généré pour nous, il est le code ASP.NET MVC standard, comme nous avons écrit tout au long de ce didacticiel. Il est destiné à vous permettre d’économiser le temps serait nécessaire sur l’écriture de code réutilisable du contrôleur et la création des vues fortement typées manuellement, mais ce n’est pas le type de code généré est peut-être affiché précédé effrayants avertissements dans les commentaires sur la façon dont vous ne doit pas modifier le code. C’est votre code, et vous êtes attendu pour le modifier.

Par conséquent, nous allons commencer par une modification rapide à la vue StoreManager Index (/ Views/StoreManager/Index.cshtml). Cet affichage d’une table qui répertorie les Albums dans notre magasin avec Modifier / détails / supprimer les liens et inclut les propriétés publiques de l’Album. Nous allons la supprimer le champ AlbumArtUrl, car il n’est pas très utile dans cet affichage. Dans &lt;table&gt; section du code de la vue, supprimer le &lt;th&gt; et &lt;td&gt; éléments autour des références à des AlbumArtUrl, comme indiqué par les lignes en surbrillance ci-dessous :

[!code-cshtml[Main](mvc-music-store-part-5/samples/sample1.cshtml)]

Le code de la vue modifiée s’affiche comme suit :

[!code-cshtml[Main](mvc-music-store-part-5/samples/sample2.cshtml)]

## <a name="a-first-look-at-the-store-manager"></a>Examine d’abord le directeur du magasin

Maintenant, exécutez l’application et accédez à/StoreManager /. Cela permet d’afficher l’Index de gestionnaire de stockage que nous avons modifié uniquement, qui contient la liste des albums dans le magasin avec des liens vers plus d’informations, modifier et supprimer.

![](mvc-music-store-part-5/_static/image3.png)

En cliquant sur le lien Modifier d’affiche un écran de modification des champs de pour l’Album, y compris les menus déroulants pour Genre et artiste.

![](mvc-music-store-part-5/_static/image4.png)

Cliquez sur le lien « Retour à la liste » en bas, puis cliquez sur le lien Détails pour un Album. Cela affiche les informations détaillées concernant un Album individuel.

![](mvc-music-store-part-5/_static/image5.png)

Là encore, cliquez sur retour à la liste, puis cliquez sur un lien de suppression. Cela affiche une boîte de dialogue confirmation, présentant les détails de l’album et vous demande si nous sommes que vous souhaitez supprimer.

![](mvc-music-store-part-5/_static/image6.png)

En cliquant sur le bouton Supprimer en bas pour supprimer l’album et vous revenez à la page d’Index, ce qui indique l’album supprimé.

Nous n’avons pas terminé avec le directeur du magasin, mais nous avons contrôleur et afficher le code pour les opérations CRUD démarrer à partir de travail.

## <a name="looking-at-the-store-manager-controller-code"></a>Le code de contrôleur du gestionnaire magasin

Le contrôleur du Gestionnaire de magasin contient une quantité importante de code. Examinons cela de haut en bas. Le contrôleur inclut certains espaces de noms standard pour un contrôleur MVC, ainsi que d’une référence à notre espace de noms de modèles. Le contrôleur a une instance privée de MusicStoreEntities, utilisé par chacune des actions de contrôleur pour accéder aux données.

[!code-csharp[Main](mvc-music-store-part-5/samples/sample3.cs)]

### <a name="store-manager-index-and-details-actions"></a>Stocker l’Index du gestionnaire et les détails des actions

La vue index récupère une liste d’Albums, y compris chaque album référencé Genre et artiste, comme nous l’avons vu précédemment lors de l’utilisation de la méthode Store Parcourir. La vue Index suit les références à des objets liés afin qu’il peut affiche chaque album nom du Genre et nom d’un artiste, donc le contrôleur est en cours efficace et interrogation de ces informations dans la demande d’origine.

[!code-csharp[Main](mvc-music-store-part-5/samples/sample4.cs)]

Action de contrôleur du contrôleur StoreManager détails fonctionne exactement comme l’action détails du contrôleur magasin nous avais précédemment écrite - il recherche de l’Album par ID à l’aide de la méthode Find(), puis le renvoie à la vue.

[!code-csharp[Main](mvc-music-store-part-5/samples/sample5.cs)]

### <a name="the-create-action-methods"></a>La création de méthodes d’Action

Les méthodes d’action de création sont un peu différentes de celles que nous avons vu jusqu’ici, car ils gèrent l’entrée du formulaire. Lorsqu’un utilisateur visite tout d’abord /StoreManager/Create/ils seront affichera un formulaire vide. Cette page HTML contient un &lt;formulaire&gt; élément qui contient la liste déroulante et zone de texte d’entrée des éléments dans lequel ils peuvent entrer les détails de l’album.

Une fois que l’utilisateur remplit les valeurs de formulaire Album, il peuvent appuyer sur le bouton « Enregistrer » pour soumettre ces modifications dans notre application à enregistrer dans la base de données. Lorsque l’utilisateur appuie sur le bouton « Enregistrer » le &lt;formulaire&gt; effectuera une requête HTTP POST vers l’URL de /StoreManager/créer et envoyer la &lt;formulaire&gt; valeurs dans le cadre de la requête HTTP-POST.

ASP.NET MVC permet de diviser facilement de la logique de ces deux scénarios d’appel URL en nous permettant d’implémenter les deux méthodes d’action « Créer » distincts au sein de notre classe StoreManagerController : une pour gérer l’initiale HTTP-GET, accédez à la /StoreManager/Create / URL et l’autre pour gérer le HTTP-POST des modifications soumises.

### <a name="passing-information-to-a-view-using-viewbag"></a>Passage des informations à une vue à l’aide de ViewBag

Nous avez utilisé l’élément ViewBag précédemment dans ce didacticiel, mais que vous n’avez pas évoquer il. ViewBag permet de passer des informations à la vue sans utiliser un objet de modèle fortement typé. Dans ce cas, notre action du contrôleur HTTP-GET modifier doit passer à la fois une liste de Genres et artistes au formulaire pour remplir les menus déroulants et la plus simple pour ce faire consiste à les retourner en tant qu’éléments de l’élément ViewBag.

L’élément ViewBag est un objet dynamique, ce qui signifie que vous pouvez taper ViewBag.Foo ou ViewBag.YourNameHere sans écrire de code pour définir ces propriétés. Dans ce cas, le code du contrôleur utilise ViewBag.GenreId et ViewBag.ArtistId afin que les valeurs de liste déroulante envoyées avec le formulaire seront GenreId et ArtistId, qui sont les propriétés de l’Album que leur définition.

Ces valeurs de liste déroulante sont renvoyées au formulaire à l’aide de l’objet SelectList, qui est généré seulement à cet effet. Cette opération est effectuée à l’aide de code similaire à celui-ci :

[!code-csharp[Main](mvc-music-store-part-5/samples/sample6.cs)]

Comme vous pouvez le voir à partir du code de méthode d’action, les trois paramètres sont utilisés pour créer cet objet :

- La liste des éléments de qu'affichage de la liste déroulante. Notez que cela n’est pas simplement une chaîne que nous transmettons une liste de Genres.
- Le paramètre suivant passé à le SelectList est la valeur sélectionnée. Cette façon dont le SelectList sait comment présélectionner un élément dans la liste. Ce sera plus facile à comprendre si vous regardez le formulaire d’édition, qui est assez similaire.
- Le dernier paramètre est la propriété à afficher. Dans ce cas, cela indique que la propriété Genre.Name est ce qui sera affiché à l’utilisateur.

Dans cette optique, puis, l’action HTTP-GET créer est assez simple - deux SelectLists sont ajoutés à l’élément ViewBag, et aucun objet de modèle n’est passé au formulaire (dans la mesure où il n’a pas encore été créé).

[!code-csharp[Main](mvc-music-store-part-5/samples/sample7.cs)]

### <a name="html-helpers-to-display-the-drop-downs-in-the-create-view"></a>Programmes d’assistance HTML pour afficher les détails de la supprimer dans la vue à créer

Étant donné que nous avons vu comment la liste déroulante de valeurs sont passées à la vue, jetons un œil rapide à la vue pour voir comment les valeurs sont affichées. Dans le code de la vue (/ Views/StoreManager/Create.cshtml), vous verrez que l’appel suivant est effectué pour afficher la liste déroulante de Genre vers le bas.

[!code-cshtml[Main](mvc-music-store-part-5/samples/sample8.cshtml)]

Il s’agit d’une application d’assistance HTML - une méthode utilitaire qui effectue une tâche courante de la vue. Programmes d’assistance HTML sont très utiles dans le maintien de notre code de vue concises et lisibles. L’application d’assistance Html.DropDownList est fournie par ASP.NET MVC, mais comme nous le verrons plus tard, il est possible de créer nos programmes d’assistance pour afficher le code que nous allons réutiliser dans notre application.

L’appel de Html.DropDownList doit simplement être invité à deux choses - cas pour obtenir la liste pour afficher et quelle valeur (le cas échéant) doit être sélectionnée au préalable. Le premier paramètre, GenreId, indique le DropDownList pour rechercher une valeur nommée GenreId dans le modèle ou un élément ViewBag. Le deuxième paramètre est utilisé pour indiquer la valeur à afficher comme initialement sélectionnée dans la liste déroulante. Étant donné que ce formulaire est un formulaire de création, il n’existe aucune valeur pour être présélectionnées et String.Empty est passé.

### <a name="handling-the-posted-form-values"></a>La gestion des valeurs de la validation de formulaire

Que nous avons abordée avant, il existe deux méthodes d’action associés à chaque formulaire. Le premier traite la demande HTTP-GET et affiche le formulaire. La seconde gère la demande HTTP-POST, qui contient les valeurs de formulaire envoyé. Remarquez qu’un attribut [HttpPost], ce qui indique à ASP.NET MVC qu’il doit répondre uniquement aux demandes HTTP-POST action du contrôleur.

[!code-csharp[Main](mvc-music-store-part-5/samples/sample9.cs)]

Cette action a quatre responsabilités :

- 1. Lire les valeurs de formulaire
- 2. Vérifiez si les valeurs de formulaire passer les règles de validation
- 3. Si l’envoi du formulaire est valide, enregistrer les données et afficher la liste de mises à jour
- 4. Si l’envoi du formulaire n’est pas valide, réafficher le formulaire avec des erreurs de validation

#### <a name="reading-form-values-with-model-binding"></a>La lecture des valeurs de formulaire avec liaison de modèle

L’action du contrôleur est le traitement d’envoi d’un formulaire qui inclut des valeurs pour GenreId et ArtistId (à partir de la zone de liste déroulante) et les valeurs de la zone de texte pour le titre, le prix et AlbumArtUrl. Bien qu’il soit possible d’accéder directement aux valeurs de formulaire, une meilleure approche consiste à utiliser les fonctionnalités de liaison de modèle intégrées à ASP.NET MVC. Lorsqu’une action de contrôleur prend un type de modèle en tant que paramètre, ASP.NET MVC va tenter de remplir un objet de ce type à l’aide des entrées de formulaire (ainsi que les valeurs d’itinéraire et de la chaîne de requête). Pour ce faire, il recherche des valeurs dont les noms correspondent aux propriétés de l’objet de modèle, par exemple, lorsque vous définissez la valeur de GenreId du nouvel objet Album, il recherche une entrée avec le nom GenreId. Lorsque vous créez des vues à l’aide des méthodes standard dans ASP.NET MVC, les formulaires seront toujours rendues à l’aide des noms de propriété en tant que noms de champ d’entrée, cela les noms de champ seront simplement correspondre à.

#### <a name="validating-the-model"></a>Validation du modèle

Le modèle est validé par un simple appel à ModelState.IsValid. Nous n’avons pas ajouté les règles de validation à la classe Album encore, nous allons faire ayant un bit - donc maintenant cette vérification pas grand-chose à faire. L’essentiel est que cette vérification ModelStat.IsValid s’adapte aux règles de validation que nous plaçons sur notre modèle, afin de futures modifications aux règles de validation ne nécessitent pas les mises à jour le code d’action de contrôleur.

#### <a name="saving-the-submitted-values"></a>Enregistrer les valeurs envoyés

Si l’envoi du formulaire de validation réussit, il est temps d’enregistrer les valeurs de la base de données. Avec Entity Framework, qui requiert simplement ajouter le modèle à la collection d’Albums et appeler SaveChanges.

[!code-csharp[Main](mvc-music-store-part-5/samples/sample10.cs)]

Entity Framework génère les commandes SQL appropriées pour conserver la valeur. Après avoir enregistré les données, nous rediriger à la liste des Albums, nous pouvons voir notre mise à jour. Pour cela, vous devez retourner RedirectToAction avec le nom de l’action de contrôleur nous à afficher. Dans ce cas, qui est la méthode d’Index.

#### <a name="displaying-invalid-form-submissions-with-validation-errors"></a>Affichage des envois de formulaire non valide avec des erreurs de Validation

Dans le cas d’entrée de formulaire non valide, les valeurs de liste déroulante sont ajoutés à l’élément ViewBag (comme dans le cas de HTTP-GET) et les valeurs du modèle lié sont transmises à la vue pour l’affichage. Erreurs de validation sont automatiquement affichés à l’aide de la @Html.ValidationMessageFor programme d’assistance HTML.

#### <a name="testing-the-create-form"></a>Le formulaire de création de test

Pour tester cela, exécutez l’application et les parcourir à /StoreManager/Create / - cette opération affiche le formulaire vierge qui a été retourné par la méthode StoreController créer HTTP-GET.

Remplir certaines valeurs, puis cliquez sur le bouton Créer pour envoyer le formulaire.

![](mvc-music-store-part-5/_static/image7.png)

![](mvc-music-store-part-5/_static/image8.png)

### <a name="handling-edits"></a>La gestion des modifications

La paire d’action de modification (HTTP-GET et HTTP-POST) est très semblable aux méthodes d’action créer que nous. Étant donné que le scénario de modification implique de travailler avec un album existant, la méthode charge de l’Album en fonction du paramètre « id », HTTP-GET à modifier transmises via l’itinéraire. Ce code pour récupérer un album par AlbumId est le même que nous avons présenté précédemment dans l’action de contrôleur de détails. Comme avec la création / HTTP-GET (méthode), la liste déroulante de valeurs sont retournées via l’élément ViewBag. Cela nous permet à retourner un Album comme notre modèle d’objet à la vue (qui est fortement typée à la classe Album) lors de la transmission des données supplémentaires (par exemple, une liste de Genres) via l’élément ViewBag.

[!code-csharp[Main](mvc-music-store-part-5/samples/sample11.cs)]

L’action HTTP-POST modifier est très similaire à l’action de création de HTTP-POST. La seule différence est que, au lieu d’ajouter un nouvel album à la base de données. Collection d’albums, nous avons observé l’instance actuelle de l’Album à l’aide de la base de données. Entry(album) et en définissant son état Modified. Cela indique à Entity Framework que nous sommes en train de modifier un album existant au lieu de créer un nouveau.

[!code-csharp[Main](mvc-music-store-part-5/samples/sample12.cs)]

Nous pouvons tester par l’application en cours d’exécution et accédant à/StoreManger /, puis en cliquant sur le lien Modifier pour un album.

![](mvc-music-store-part-5/_static/image9.png)

Cela affiche le formulaire d’édition indiqué par la méthode HTTP-GET modifier. Remplir certaines valeurs, puis cliquez sur le bouton Enregistrer.

![](mvc-music-store-part-5/_static/image10.png)

Publie le formulaire, enregistre les valeurs, il nous ramène à la liste des albums, indiquant que les valeurs ont été mis à jour.

![](mvc-music-store-part-5/_static/image11.png)

### <a name="handling-deletion"></a>Suppression de la gestion

Suppression suit le même modèle que modifier et créer, à l’aide d’action d’un contrôleur pour afficher l’écran de confirmation et une autre action de contrôleur pour gérer l’envoi du formulaire.

L’action du contrôleur HTTP-GET Delete est identique à notre action de contrôleur Store Manager détails précédente.

[!code-csharp[Main](mvc-music-store-part-5/samples/sample13.cs)]

Pour un type Album, en utilisant le modèle de contenu de la vue Delete, nous affichons un formulaire qui est fortement typé.

![](mvc-music-store-part-5/_static/image12.png)

Le modèle de suppression affiche tous les champs pour le modèle, mais nous pouvons simplifier mal que vers le bas. Modifiez l’affichage du code dans /Views/StoreManager/Delete.cshtml comme suit.

[!code-cshtml[Main](mvc-music-store-part-5/samples/sample14.cshtml)]

Cela affiche une confirmation de suppression simplifiée.

![](mvc-music-store-part-5/_static/image13.png)

En cliquant sur le bouton Supprimer entraîne le formulaire à être publié sur le serveur qui exécute l’action DeleteConfirmed.

[!code-csharp[Main](mvc-music-store-part-5/samples/sample15.cs)]

Notre Action du contrôleur HTTP-POST supprimer effectue les actions suivantes :

- 1. Charge l’Album par ID
- 2. Il supprime l’album et enregistrer les modifications
- 3. Effectue une redirection vers l’Index, indiquant que l’Album a été supprimé de la liste

Pour tester cela, exécutez l’application et accédez à /StoreManager. Sélectionnez un album à partir de la liste et cliquez sur le lien Supprimer.

![](mvc-music-store-part-5/_static/image14.png)

Cela affiche notre écran de confirmation de suppression.

![](mvc-music-store-part-5/_static/image15.png)

En cliquant sur le bouton Supprimer supprime l’album et nous ramène à la page d’Index de gestionnaire de magasin, ce qui indique que l’album a été supprimé.

![](mvc-music-store-part-5/_static/image16.png)

### <a name="using-a-custom-html-helper-to-truncate-text"></a>À l’aide d’un programme d’assistance HTML personnalisé pour le texte tronqué

Nous avons un problème potentiel avec notre page d’Index de gestionnaire de magasin. Notre propriétés du titre de l’Album et le nom de l’auteur peuvent tous deux être suffisamment longue qu’ils pourrait lever off notre table mise en forme. Nous allons créer un programme d’assistance HTML personnalisé pour nous permettent de facilement tronquer ces autres propriétés et dans nos vues.

![](mvc-music-store-part-5/_static/image17.png)

De Razor @helper syntaxe devenue assez facile à créer vos propres fonctions d’assistance pour une utilisation dans les vues. Ouvrez l’affichage /Views/StoreManager/Index.cshtml et ajoutez le code suivant directement après le @model ligne.

[!code-cshtml[Main](mvc-music-store-part-5/samples/sample16.cshtml)]

Cette méthode d’assistance prend une chaîne et une longueur maximale à autoriser. Si le texte fourni est inférieur à la longueur spécifiée, le programme d’assistance renvoie en tant que-est. S’il est plus long, il tronque le texte et restitue «... » pour le reste.

Maintenant, nous pouvons utiliser notre programme d’assistance de troncation pour vous assurer que le titre de l’Album et le nom de l’auteur des propriétés sont moins de 25 caractères. Le code de la vue complète à l’aide de notre nouveau programme d’assistance Truncate apparaît ci-dessous.

[!code-cshtml[Main](mvc-music-store-part-5/samples/sample17.cshtml)]

Maintenant lorsque nous parcourir l’URL /StoreManager/, les albums et les titres sont conservées sous notre longueurs maximales.

![](mvc-music-store-part-5/_static/image18.png)

Remarque : Cela indique un cas simple de la création et à l’aide d’un programme d’assistance dans une vue. Pour en savoir plus sur la création de programmes d’assistance que vous pouvez utiliser tout au long de votre site, consultez mon blog : [http://bit.ly/mvc3-helper-options](http://bit.ly/mvc3-helper-options)


>[!div class="step-by-step"]
[Précédent](mvc-music-store-part-4.md)
[Suivant](mvc-music-store-part-6.md)
