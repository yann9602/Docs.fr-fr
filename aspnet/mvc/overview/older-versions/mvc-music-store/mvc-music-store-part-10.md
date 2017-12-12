---
uid: mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-10
title: "Partie 10 : Les mises à jour finales à la Navigation et la conception de Site, Conclusion | Documents Microsoft"
author: jongalloway
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 10 couvre les mises à jour finales à la Navigation et s..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/21/2011
ms.topic: article
ms.assetid: 0c6e4c2f-fcdb-4978-9656-1990c6f15727
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-10
msc.type: authoredcontent
ms.openlocfilehash: af08039de2d810948b9ab64974111b0346c7fa0f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-10-final-updates-to-navigation-and-site-design-conclusion"></a>Partie 10 : Les mises à jour finales à la Navigation et la conception de Site, Conclusion
====================
par [Jon Galloway](https://github.com/jongalloway)

> Le magasin de musique MVC est une application du didacticiel qui présente et explique étape par étape comment utiliser ASP.NET MVC et Visual Studio pour le développement web.  
>   
> Le magasin de musique MVC est une implémentation de magasin exemple léger qui vend des albums de musique en ligne et implémente l’administration de site de base, authentification de l’utilisateur et les fonctionnalités de panier d’achat.  
>   
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 10 couvre les mises à jour finales à la Navigation et la conception de Site, Conclusion.


Nous avons terminé toutes les fonctionnalités principales de notre site, mais nous avons toujours certaines fonctionnalités à ajouter à la navigation du site, la page d’accueil et la page Rechercher un magasin.

## <a name="creating-the-shopping-cart-summary-partial-view"></a>Création de la vue partielle résumé panier d’achat

Vous souhaitez exposer le nombre d’éléments dans le panier d’achat de l’utilisateur sur l’intégralité du site.

![](mvc-music-store-part-10/_static/image1.png)

Nous pouvons facilement implémenter cela en créant une vue partielle, qui est ajoutée à notre Site.master.

Comme indiqué précédemment, le contrôleur ShoppingCart inclut une méthode d’action CartSummary qui retourne une vue partielle :

[!code-csharp[Main](mvc-music-store-part-10/samples/sample1.cs)]

Pour créer la vue partielle CartSummary, avec le bouton droit sur le dossier vues/ShoppingCart et sélectionnez Ajouter une vue. Nom de la vue CartSummary et vérifiez la case à cocher « Créer une vue partielle » comme indiqué ci-dessous.

![](mvc-music-store-part-10/_static/image2.png)

La vue partielle CartSummary est très simple : il s’agit simplement d’un lien vers la vue ShoppingCart Index qui indique le nombre d’éléments dans le panier d’achat. Le code complet pour CartSummary.cshtml est la suivante :

[!code-cshtml[Main](mvc-music-store-part-10/samples/sample2.cshtml)]

Nous pouvons inclure une vue partielle dans n’importe quelle page dans le site, y compris le maître de Site, à l’aide de la méthode Html.RenderAction. RenderAction nous oblige à spécifier le nom de l’Action (« CartSummary ») et le nom du contrôleur (« ShoppingCart ») comme ci-dessous.

[!code-cshtml[Main](mvc-music-store-part-10/samples/sample3.cshtml)]

Avant d’ajouter cela à la disposition du site, nous allons également créer le Menu de Genre, par conséquent, nous pouvons toutes nos mises à jour Site.master en même temps.

## <a name="creating-the-genre-menu-partial-view"></a>Création de la vue partielle du Menu Genre

Nous pouvons effectuer beaucoup plus facile pour nos utilisateurs de naviguer dans le magasin en ajoutant un Menu de Genre qui répertorie tous les Genres disponibles dans notre magasin.

![](mvc-music-store-part-10/_static/image3.png)

Nous allons suivre les mêmes étapes également créent une vue partielle GenreMenu, et nous pouvons ensuite les ajouter au contrôleur de Site. Tout d’abord, ajoutez l’action de contrôleur GenreMenu suivante à la StoreController :

[!code-csharp[Main](mvc-music-store-part-10/samples/sample4.cs)]

Cette action renvoie une liste de Genres qui sera affiché par la vue partielle, ce qui nous allons créer ensuite.

*Remarque : Nous avons ajouté l’attribut [ChildActionOnly] à cette action de contrôleur, ce qui indique que nous voulons uniquement cette action pour être utilisée à partir d’une vue partielle. Cet attribut empêche l’action de contrôleur à partir d’en cours d’exécution en accédant à /Store/GenreMenu. Cela n’est pas nécessaire pour les vues partielles, mais il est conseillé, étant donné que nous voulons que nos actions de contrôleur sont utilisées comme nous avons l’intention. Nous sommes également retourner PartialView plutôt que de vue, qui informe le moteur de vue qu’il ne doit pas utiliser la mise en page pour cette vue, car elle est incluse dans les autres modes.*

Avec le bouton droit sur l’action du contrôleur GenreMenu et créer une vue partielle nommée GenreMenu fortement typé à l’aide de la classe de données d’affichage Genre comme indiqué ci-dessous.

![](mvc-music-store-part-10/_static/image4.png)

Mettre à jour le code de la vue de la vue partielle de GenreMenu afficher les éléments à l’aide d’une liste non triée comme suit.

[!code-cshtml[Main](mvc-music-store-part-10/samples/sample5.cshtml)]

## <a name="updating-site-layout-to-display-our-partial-views"></a>Mise à jour de la mise en page de Site pour afficher ses vues partielles

Nous pouvons ajouter nos vues partielles à la disposition de Site (/vues/Shared/\_Layout.cshtml) en appelant Html.RenderAction(). Nous allons ajouter tous les deux dans, ainsi que des balises supplémentaires pour les afficher, comme indiqué ci-dessous :

[!code-cshtml[Main](mvc-music-store-part-10/samples/sample6.cshtml)]

Lorsque nous exécutons l’application, nous allons maintenant voir le Genre dans la zone de navigation gauche et le résumé du panier en haut.

## <a name="update-to-the-store-browse-page"></a>Mettre à jour vers la page Rechercher un magasin

La page Rechercher un magasin est fonctionnelle, mais ne ressemble pas très bien. Nous pouvons mettre à jour de la page pour afficher les albums dans une disposition de mieux en mettant à jour le code de vue (trouvé dans /Views/Store/Browse.cshtml) comme suit :

[!code-cshtml[Main](mvc-music-store-part-10/samples/sample7.cshtml)]

Ici, nous effectuons utiliser Url.Action plutôt que Html.ActionLink afin que nous pouvons appliquer une mise en forme spéciale sur le lien pour inclure l’illustration de l’album.

*Remarque : Nous affichons un garde album générique pour ces albums. Ces informations sont stockées dans la base de données et peut être modifiées via le Gestionnaire de magasin. Vous pouvez ajouter vos propres graphiques.*

Maintenant lorsque nous accédez à un Genre, nous allons voir les albums affichées dans une grille avec l’illustration de l’album.

![](mvc-music-store-part-10/_static/image5.png)

## <a name="updating-the-home-page-to-show-top-selling-albums"></a>Mise à jour de la Page d’accueil pour afficher des Albums de vente de haut

Nous souhaitons notre albums sur la page d’accueil d’augmenter les ventes de meilleurs. Nous allons effectuer certaines mises à jour notre HomeController pour gérer cela, ajoutez dans certains graphiques supplémentaires également.

Tout d’abord, nous allons ajouter une propriété de navigation à la classe Album afin que EntityFramework sait qu’ils sont associés. Les deux dernières lignes de notre **Album** classe doit maintenant ressembler à ceci :

[!code-csharp[Main](mvc-music-store-part-10/samples/sample8.cs)]

*Remarque : Vous devrez ajouter une à l’aide de l’instruction pour placer dans l’espace de noms System.Collections.Generic.*

Tout d’abord, nous allons ajouter un champ storeDB et le MvcMusicStore.Models à l’aide des instructions, comme dans nos autres contrôleurs. Ensuite, nous allons ajouter la méthode suivante pour le fichier HomeController qui interroge notre base de données pour trouver les meilleurs albums ventes en fonction de OrderDetails.

[!code-csharp[Main](mvc-music-store-part-10/samples/sample9.cs)]

Il s’agit d’une méthode privée, étant donné que nous ne voulons pas rendre disponible en tant qu’une action de contrôleur. Nous allons l’inclure dans le HomeController par souci de simplicité, mais il est recommandé de déplacer votre logique métier dans les classes de service distinct comme il convient.

Cela en place, nous pouvons mettre à jour l’action de contrôleur d’Index pour interroger les 5 premières albums de vente et les renvoyer à la vue.

[!code-csharp[Main](mvc-music-store-part-10/samples/sample10.cs)]

Le code complet pour le fichier HomeController mis à jour est comme indiqué ci-dessous.

[!code-csharp[Main](mvc-music-store-part-10/samples/sample11.cs)]

Enfin, nous devrons mettre à jour notre vue accueil Index afin qu’il peut afficher une liste d’albums par le type de modèle de mise à jour et l’ajout de la liste des albums vers le bas. Nous allons prendre cette opportunité pour également ajouter un titre et une section de promotion à la page.

[!code-cshtml[Main](mvc-music-store-part-10/samples/sample12.cshtml)]

Maintenant lorsque nous exécutons l’application, nous allons voir notre page d’accueil mises à jour avec les meilleurs albums ventes et notre message promotionnel.

![](mvc-music-store-part-10/_static/image1.jpg)

## <a name="conclusion"></a>Conclusion

Nous avons vu que que ASP.NET MVC facilite la création d’un site Web sophistiqué avec un accès de base de données, l’appartenance, AJAX, etc. assez rapidement. Nous espérons que ce didacticiel vous a accordé les outils que vous avez besoin pour commencer à créer votre propre MVC ASP.NET applications !


>[!div class="step-by-step"]
[Précédent](mvc-music-store-part-9.md)
