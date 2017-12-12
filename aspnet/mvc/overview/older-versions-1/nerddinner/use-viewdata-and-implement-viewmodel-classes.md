---
uid: mvc/overview/older-versions-1/nerddinner/use-viewdata-and-implement-viewmodel-classes
title: "Utilisez ViewData et implémentez ViewModel Classes | Documents Microsoft"
author: microsoft
description: "Étape 6 montre comment activer la prise en charge de la modification de scénarios, de forme plus riche et présente également les deux approches qui peuvent être utilisés pour passer des données à partir des contrôleurs à des vues :..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: 5755ec4c-60f1-4057-9ec0-3a5de3a20e23
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/use-viewdata-and-implement-viewmodel-classes
msc.type: authoredcontent
ms.openlocfilehash: 36b9e87cc24f74f7f2cc592afb5102709b598f74
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="use-viewdata-and-implement-viewmodel-classes"></a>Utilisez ViewData et implémenter les Classes ViewModel
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> Il s’agit d’étape 6 a gratuit [« « l’application NerdDinner](introducing-the-nerddinner-tutorial.md) qui parcours à comment générer un petit, mais se termine, application web à l’aide d’ASP.NET MVC 1.
> 
> Étape 6 montre comment activer la prise en charge de la modification de scénarios, de forme plus riche et présente également les deux approches qui peuvent être utilisés pour passer des données à partir des contrôleurs à des vues : ViewData et ViewModel.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-step-6-viewdata-and-viewmodel"></a>NerdDinner étape 6 : ViewData et ViewModel

Nous avons couvert un nombre de scénarios de publication de formulaire et décrit comment implémenter créer, mettre à jour et supprimer la prise en charge (CRUD). Maintenant nous prendre notre implémentation DinnersController complémentaires et activer la prise en charge de la modification de scénarios de formulaire plus riche. Lors de cette opération, nous verrons deux approches qui peuvent être utilisés pour passer des données à partir des contrôleurs à des vues : ViewData et ViewModel.

### <a name="passing-data-from-controllers-to-view-templates"></a>Passage de données à partir des contrôleurs pour afficher les modèles

Une des caractéristiques de définition du modèle MVC est stricte « séparation des responsabilités » il permet d’appliquer entre les différents composants d’une application. Les modèles, les contrôleurs et les vues chacun ont bien définie de rôles et responsabilités, et ils communiquent entre eux de façons bien définies. Cela permet de promouvoir la testabilité et réutilisation du code.

Quand une classe de contrôleur décide de restituer une réponse HTML à un client, il est responsable en fournissant explicitement pour le modèle d’affichage de toutes les données nécessaires au rendu de la réponse. Afficher les modèles ne doivent jamais exécuter toute logique d’application ou de récupération de données – et doivent se pour disposer uniquement le code de rendu qui est basée sur les modèle/des données transmises par le contrôleur de limiter à la place.

Maintenant les données du modèle passé par notre DinnersController classe nos modèles de vue est simple et directe : une liste d’objets dîner dans le cas Index() et un dîner unique de l’objet dans le cas Details(), Edit(), Create() et Delete(). Comme nous ajouter davantage de capacités de l’interface utilisateur à notre application, nous allons souvent ont besoin de transmettre plus que ces données pour effectuer le rendu des réponses HTML dans nos modèles de vue. Par exemple, nous souhaitons pouvons modifier le champ « Pays » au sein de notre modifier et créer des vues d’en cours d’une zone de texte HTML à une liste déroulante. Au lieu de coder en dur la liste déroulante des noms de pays dans le modèle d’affichage, nous pouvons si vous le souhaitez générer à partir d’une liste de pays pris en charge qui nous remplir dynamiquement. Nous devons permettent de passer à la fois l’objet Dinner *et* la liste des pays pris en charge à partir de notre contrôleur à nos modèles de vue.

Nous allons étudier deux façons, que nous pouvons effectuer cette opération.

### <a name="using-the-viewdata-dictionary"></a>À l’aide du dictionnaire ViewData

La classe de base du contrôleur expose une propriété au dictionnaire « ViewData » qui peut être utilisée pour passer des éléments de données à partir des contrôleurs à des vues.

Par exemple, pour prendre en charge le scénario où nous voulons modifier la zone de texte « Pays » dans la vue d’édition ne soient pas d’une zone de texte HTML à une liste déroulante, nous pouvons mettre à jour notre méthode d’action Edit() pour transmettre (en plus d’un objet Dinner) un objet SelectList qui peut être utilisé comme mesure odèle de dropdownlist pays.

[!code-csharp[Main](use-viewdata-and-implement-viewmodel-classes/samples/sample1.cs)]

Le constructeur de la SelectList ci-dessus accepte une liste de comtés pour remplir la liste-downlist avec, ainsi que la valeur actuellement sélectionnée.

Nous pouvons ensuite mettre à jour notre modèle de vue Edit.aspx pour utiliser la méthode d’assistance Html.DropDownList() au lieu de la méthode d’assistance Html.TextBox() que nous avons utilisé précédemment :

[!code-aspx[Main](use-viewdata-and-implement-viewmodel-classes/samples/sample2.aspx)]

La méthode d’assistance Html.DropDownList() ci-dessus accepte deux paramètres. Le premier est le nom de l’élément de formulaire HTML en sortie. Le deuxième est le modèle « SelectList » nous avons transmis via le dictionnaire ViewData. Nous utilisons c# mot clé « as » pour effectuer un cast de type au sein du dictionnaire en tant qu’un SelectList.

Et maintenant quand nous exécutons notre accès aux applications et le */Dinners/Edit/1* URL dans votre navigateur, nous allons voir que notre interface utilisateur de modification a été mis à jour pour afficher une liste déroulante des pays au lieu d’une zone de texte :

![](use-viewdata-and-implement-viewmodel-classes/_static/image1.png)

Étant donné que nous avons rendu également le modèle de vue de modification de la méthode HTTP-POST modifier (dans les scénarios de cas d’erreur), nous vous souhaitez vous assurer que nous avons également mettre à jour cette méthode pour ajouter le SelectList à ViewData lorsque le modèle d’affichage est rendu dans les scénarios d’erreur :

[!code-csharp[Main](use-viewdata-and-implement-viewmodel-classes/samples/sample3.cs)]

Et maintenant notre scénario de modification DinnersController prend en charge une liste déroulante.

### <a name="using-a-viewmodel-pattern"></a>Utilisation d’un modèle ViewModel

L’approche de dictionnaire ViewData présente l’avantage d’être relativement rapide et facile à implémenter. Certains développeurs n’aiment pas à l’aide de dictionnaires basés sur chaîne, cependant, étant donné que les fautes de frappe peuvent entraîner des erreurs qui ne sont pas interceptées au moment de la compilation. Le dictionnaire ViewData non typé requiert également à l’aide de l’opérateur « as » ou de conversion lors de l’utilisation d’un langage fortement typé tel que c# dans un modèle d’affichage.

Une autre approche que nous pourrions utiliser est un est souvent appelé le motif « View ». Lors de l’utilisation de ce modèle, nous créons des classes fortement typées qui sont optimisés pour nos scénarios d’affichage spécifiques, et qui expose les propriétés pour le valeurs/contenu dynamique nécessaire par nos modèles de vue. Nos classes de contrôleur peuvent remplir, puis passer ces classes optimisées en vue de notre modèle d’affichage à utiliser. Cela permet la sécurité de type, la vérification de la compilation et intellisense de l’éditeur dans les modèles d’affichage.

Par exemple, pour activer le formulaire dinner éditions scénarios que nous pouvons créer un « DinnerFormViewModel » classe comme ci-dessous qui expose deux propriétés fortement typées : un objet dîner et le modèle SelectList requis pour remplir le dropdownlist pays :

[!code-csharp[Main](use-viewdata-and-implement-viewmodel-classes/samples/sample4.cs)]

Nous pouvons ensuite mettre à jour notre méthode d’action Edit() pour créer le DinnerFormViewModel à l’aide de l’objet Dinner que nous récupérons à partir de notre référentiel et passez-lui à notre modèle de vue :

[!code-csharp[Main](use-viewdata-and-implement-viewmodel-classes/samples/sample5.cs)]

Nous allons ensuite la mise à jour notre modèle d’affichage afin qu’elle attend un « DinnerFormViewModel » au lieu d’un « dîner » de l’objet en modifiant l’attribut « inherits » en haut de la page edit.aspx comme suit :

[!code-cshtml[Main](use-viewdata-and-implement-viewmodel-classes/samples/sample6.cshtml)]

Une fois que cela, nous, intellisense de la propriété « Modèle » au sein de notre modèle de vue sera mis à jour pour refléter le modèle d’objet du type DinnerFormViewModel que nous sommes en lui passant :

![](use-viewdata-and-implement-viewmodel-classes/_static/image2.png)

![](use-viewdata-and-implement-viewmodel-classes/_static/image3.png)

Nous pouvons ensuite mettre à jour notre code afficher travailler dessus. Remarquez ci-dessous comment nous modifions pas les noms des éléments d’entrée, nous allons créer (les éléments de formulaire seront toujours nommés « Title », « Pays »), mais nous allons mettre à jour les méthodes de programme d’assistance HTML pour extraire les valeurs à l’aide de la classe DinnerFormViewModel :

[!code-aspx[Main](use-viewdata-and-implement-viewmodel-classes/samples/sample7.aspx)]

Nous allons également mettre à jour notre méthode post de modifier pour utiliser la classe DinnerFormViewModel lors du rendu des erreurs :

[!code-csharp[Main](use-viewdata-and-implement-viewmodel-classes/samples/sample8.cs)]

Nous pouvons également mettre à jour nos méthodes d’action Create() réutiliser le même *DinnerFormViewModel* classe pour permettre les pays DropDownList dans celles ainsi. Voici l’implémentation HTTP-GET :

[!code-csharp[Main](use-viewdata-and-implement-viewmodel-classes/samples/sample9.cs)]

Voici l’implémentation de la méthode HTTP-POST créer :

[!code-csharp[Main](use-viewdata-and-implement-viewmodel-classes/samples/sample10.cs)]

En maintenant nos écrans modifier et créer des listes déroulantes pour la sélection du pays.

### <a name="custom-shaped-viewmodel-classes"></a>Classes de ViewModel en forme personnalisée

Dans le scénario ci-dessus, notre classe DinnerFormViewModel expose directement l’objet de modèle Dinner en tant que propriété, ainsi que d’une propriété de modèle SelectList prise en charge. Cette approche fonctionne pour les scénarios où l’interface utilisateur HTML que nous voulons créer au sein de notre modèle d’affichage correspond relativement à nos objets de modèle de domaine.

Pour les scénarios où cela n’est pas le cas, une option que vous pouvez utiliser consiste à créer une classe de ViewModel en forme personnalisée dont le modèle objet est plus optimisé pour la consommation par la vue, et qui peut se présenter complètement différent de l’objet de modèle de domaine sous-jacent. Par exemple, elle peut potentiellement exposer les noms des propriétés et/ou des propriétés d’agrégation recueillies à partir de plusieurs objets de modèle.

Les classes ViewModel en forme personnalisées peuvent être utilisées à la fois pour passer des données à partir des contrôleurs à des vues à restituer, ainsi que pour aider à gérer les données de formulaire publiées sur la méthode d’action d’un contrôleur. Pour ce scénario plus loin, vous disposez peut-être la méthode d’action mettre à jour un objet ViewModel avec les données de formulaire publié, puis utiliser l’instance ViewModel mapper ou récupérer un objet de modèle de domaine réel.

Classes de ViewModel en forme personnalisée peuvent fournir une grande souplesse et sont quelque chose à examiner chaque fois que vous recherchez le code de rendu dans vos modèles d’affichage ou le code de validation de formulaire à l’intérieur de vos méthodes d’action commence à se compliquer trop. Il s’agit souvent un signe que vos modèles de domaine correctement ne correspondent pas à l’interface utilisateur que vous générez, et qu’une classe de ViewModel intermédiaire en forme personnalisée peut aider.

### <a name="next-step"></a>Étape suivante

Nous allons maintenant voir comment nous pouvons utiliser aucun et les pages maîtres pour réutiliser et partager l’interface utilisateur dans notre application.

>[!div class="step-by-step"]
[Précédent](provide-crud-create-read-update-delete-data-form-entry-support.md)
[Suivant](re-use-ui-using-master-pages-and-partials.md)
