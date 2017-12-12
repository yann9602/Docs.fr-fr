---
uid: mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6
title: "Partie 6 : À l’aide des Annotations de données pour la Validation de modèle | Documents Microsoft"
author: jongalloway
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 6 traite à l’aide des Annotations de données pour le modèle V..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/21/2011
ms.topic: article
ms.assetid: b3193d33-2d0b-4d98-9712-58bd897c62ec
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6
msc.type: authoredcontent
ms.openlocfilehash: b2083d5604741a0142f504f92779c9f931d0d6d9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-6-using-data-annotations-for-model-validation"></a>Partie 6 : À l’aide des Annotations de données pour la Validation de modèle
====================
par [Jon Galloway](https://github.com/jongalloway)

> Le magasin de musique MVC est une application du didacticiel qui présente et explique étape par étape comment utiliser ASP.NET MVC et Visual Studio pour le développement web.  
>   
> Le magasin de musique MVC est une implémentation de magasin exemple léger qui vend des albums de musique en ligne et implémente l’administration de site de base, authentification de l’utilisateur et les fonctionnalités de panier d’achat.  
>   
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 6 couvre à l’aide des Annotations de données pour la Validation de modèle.


Nous avons un problème majeur avec notre formulaire de créer et modifier : ils ne sont pas effectuées aucune validation. Nous pouvons faire laisser vides les champs obligatoires ou des lettres de type dans le champ prix, et la première erreur, que nous verrons provient de la base de données.

Nous pouvons facilement ajouter une validation à notre application en ajoutant des Annotations de données à notre classes du modèle. Annotations de données nous permettent de décrire les règles que nous souhaitons appliqués aux propriétés de notre modèle, et ASP.NET MVC s’occupe de l’appliquer et d’afficher les messages appropriés aux utilisateurs.

## <a name="adding-validation-to-our-album-forms"></a>Ajout d’une Validation à notre Forms Album

Nous allons utiliser les attributs de l’Annotation de données suivants :

- **Requis** – indique que la propriété est un champ obligatoire
- **DisplayName** – définit le texte que nous souhaitons utilisés sur les champs de formulaire et des messages de validation
- **StringLength** – définit une longueur maximale pour un champ de chaîne
- **Plage** – donne la valeur minimale et maximale pour un champ numérique
- **Lier** – répertorie les champs à exclure ou inclure lors de la liaison de paramètre ou la forme de valeurs aux propriétés de modèle
- **ScaffoldColumn** : autorise le masquage des champs à partir de formulaires de l’éditeur

*Remarque : Pour plus d’informations sur la Validation de modèle à l’aide des attributs d’annotations de données, consultez la documentation MSDN à l’adresse*[`https://go.microsoft.com/fwlink/?LinkId=159063`](https://go.microsoft.com/fwlink/?LinkId=159063)

Ouvrez la classe Album et ajoutez le code suivant *à l’aide de* instructions au début.

[!code-csharp[Main](mvc-music-store-part-6/samples/sample1.cs)]

Ensuite, mettez à jour les propriétés pour ajouter des attributs d’affichage et de validation comme indiqué ci-dessous.

[!code-csharp[Main](mvc-music-store-part-6/samples/sample2.cs)]

Pendant que nous sommes il, nous avons aussi modifié le Genre et artiste propriétés virtuelles. Cela permet à Entity Framework charger en différé-les si nécessaire.

[!code-csharp[Main](mvc-music-store-part-6/samples/sample3.cs)]

Après avoir ajouté des ces attributs à notre modèle Album, notre écran de créer et modifier immédiatement la validation des champs et en utilisant les noms d’affichage, nous avons choisi (par exemple, Album Art Url au lieu de AlbumArtUrl). Exécutez l’application et accédez à /StoreManager/Create.

![](mvc-music-store-part-6/_static/image1.png)

Ensuite, nous allons arrêter des règles de validation. Entrez un prix de 0 et ne renseignez pas le titre. Après avoir cliqué sur le bouton Créer, nous allons voir le formulaire affiché avec les messages d’erreur indiquant les champs qui ne répondait pas aux règles de validation que nous avons défini.

![](mvc-music-store-part-6/_static/image2.png)

## <a name="testing-the-client-side-validation"></a>Test de la Validation côté Client

La validation côté serveur est très importante du point de vue de l’application, car les utilisateurs peuvent contourner la validation côté client. Toutefois, les formulaires de page Web qui implémentent uniquement la validation côté serveur présentent trois problèmes importants.

1. L’utilisateur se trouve en attente pour le formulaire à valider, validé sur le serveur et pour la réponse à envoyer au navigateur.
2. L’utilisateur n’immédiatement obtenir des commentaires lorsqu’ils corriger un champ pour qu’il passe désormais les règles de validation.
3. Nous sommes gaspiller des ressources du serveur pour exécuter la logique de validation au lieu de tirer parti du navigateur de l’utilisateur.

Heureusement, les modèles de vue de structure d’ASP.NET MVC 3 ont la validation côté client intégrée, ne nécessitant aucun travail supplémentaire que ce soit.

Tapez une lettre unique dans le champ titre est conforme aux exigences de validation, par conséquent, le message de validation est supprimé immédiatement.

![](mvc-music-store-part-6/_static/image3.png)


>[!div class="step-by-step"]
[Précédent](mvc-music-store-part-5.md)
[Suivant](mvc-music-store-part-7.md)
