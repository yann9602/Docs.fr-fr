---
uid: mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-9
title: "Partie 9 : Enregistrement et extraction | Documents Microsoft"
author: jongalloway
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 9 couvre l’inscription et l’extraction."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/21/2011
ms.topic: article
ms.assetid: d65c5c2b-a039-463f-ad29-25cf9fb7a1ba
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-9
msc.type: authoredcontent
ms.openlocfilehash: 799985f889b1063c53df7bce365ae3989809ba79
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-9-registration-and-checkout"></a>Partie 9 : Enregistrement et extraction
====================
par [Jon Galloway](https://github.com/jongalloway)

> Le magasin de musique MVC est une application du didacticiel qui présente et explique étape par étape comment utiliser ASP.NET MVC et Visual Studio pour le développement web.  
>   
> Le magasin de musique MVC est une implémentation de magasin exemple léger qui vend des albums de musique en ligne et implémente l’administration de site de base, authentification de l’utilisateur et les fonctionnalités de panier d’achat.  
>   
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 9 couvre l’inscription et l’extraction.


Dans cette section, nous allons créer un CheckoutController qui collectera les adresses du client et les informations de paiement. Nous oblige les utilisateurs à inscrire avec notre site avant la récupération, donc ce contrôleur nécessite une autorisation.

Les utilisateurs pour accéder à la caisse à partir de leur panier d’achat en cliquant sur le bouton « Valider ».

![](mvc-music-store-part-9/_static/image1.jpg)

Si l’utilisateur n’est pas connecté, il seront invité à.

![](mvc-music-store-part-9/_static/image1.png)

Lors de la connexion réussite, l’utilisateur s’affiche alors la vue de l’adresse et de paiement.

![](mvc-music-store-part-9/_static/image2.png)

Une fois qu’ils ont rempli le formulaire et l’ordre, elles sont affichées à l’écran de confirmation de commande.

![](mvc-music-store-part-9/_static/image3.png)

Essayez d’afficher une commande inexistant ou une commande qui n’appartient pas à vous affiche la vue de l’erreur.

![](mvc-music-store-part-9/_static/image4.png)

## <a name="migrating-the-shopping-cart"></a>Migration du panier d’achat

Pendant le processus d’achat est anonyme, lorsque l’utilisateur clique sur le bouton d’extraction, ils seront requises pour l’inscription et connexion. Utilisateurs attendent que nous met à jour leurs informations de panier d’achat entre les visites, donc nous devrons à associer les informations du panier d’un utilisateur lorsqu’ils suivent l’inscription ou la connexion.

Il s’agit en fait très simple à faire, comme notre classe ShoppingCart possède déjà une méthode qui associe tous les éléments dans le panier d’achat actuelle avec un nom d’utilisateur. Vous devrez simplement appeler cette méthode lorsqu’un utilisateur a terminé l’inscription ou la connexion.

Ouvrez le **AccountController** classe que nous avons ajoutées lorsque nous étions vous configurez l’appartenance et l’autorisation. Ajouter un à l’aide d’instruction faisant référence à MvcMusicStore.Models, puis ajoutez la méthode MigrateShoppingCart suivante :

[!code-csharp[Main](mvc-music-store-part-9/samples/sample1.cs)]

Ensuite, modifiez l’action d’ouverture de session pour appeler MigrateShoppingCart après la validation de l’utilisateur, comme indiqué ci-dessous :

[!code-csharp[Main](mvc-music-store-part-9/samples/sample2.cs)]

Apporter la même modification au Registre de valider l’action, immédiatement après que le compte d’utilisateur est créé avec succès :

[!code-csharp[Main](mvc-music-store-part-9/samples/sample3.cs)]

C’est tout - maintenant un panier d’achat anonyme est automatiquement transférée à un compte d’utilisateur après la connexion ou l’inscription réussie.

## <a name="creating-the-checkoutcontroller"></a>Création de la CheckoutController

Avec le bouton droit sur le dossier contrôleurs et ajoutez un nouveau contrôleur au projet nommé CheckoutController à l’aide du modèle de contrôleur vide.

![](mvc-music-store-part-9/_static/image5.png)

Tout d’abord, ajoutez l’attribut Authorize au-dessus de la déclaration de classe de contrôleur pour obliger les utilisateurs à inscrire avant l’extraction :

[!code-csharp[Main](mvc-music-store-part-9/samples/sample4.cs)]

*Remarque : Ceci est similaire à la modification que nous avons effectuées précédemment pour le StoreManagerController, mais dans ce cas l’attribut Authorize nécessaire que l’utilisateur soit dans un rôle d’administrateur. Dans le contrôleur d’extraction, nous allons nécessitant l’utilisateur est connecté, mais ne sont pas exiger qu’ils soient des administrateurs.*

Par souci de simplicité, nous ne sera pas gérer les informations de paiement dans ce didacticiel. Au lieu de cela, nous autorisons les utilisateurs à extraire à l’aide d’un code promotionnel. Nous allons stocker ce code promotionnel à l’aide d’une constante nommée du PromoCode.

Comme dans le StoreController, nous allons déclarer un champ pour stocker une instance de la classe MusicStoreEntities, nommée storeDB. Pour rendre utilisent la classe MusicStoreEntities, nous devons ajouter une à l’aide de l’instruction pour l’espace de noms MvcMusicStore.Models. La partie supérieure de notre contrôleur retrait s’affiche ci-dessous.

[!code-csharp[Main](mvc-music-store-part-9/samples/sample5.cs)]

Le CheckoutController aura les actions de contrôleur suivantes :

**AddressAndPayment (méthode GET)** affiche un formulaire pour permettre à l’utilisateur à entrer leurs informations.

**AddressAndPayment (méthode POST)** valide l’entrée et traiter la commande.

**Complète** s’affichera une fois que l’utilisateur a terminé avec succès le processus de validation. Cette vue doit contenir le numéro de commande de l’utilisateur, confirmation.

Tout d’abord, nous allons renommer l’action de contrôleur d’Index (qui a été générée lorsque nous avons créé le contrôleur) à AddressAndPayment. Cette action de contrôleur affiche simplement du formulaire de commande, il est inutile de toutes les informations de modèle.

[!code-csharp[Main](mvc-music-store-part-9/samples/sample6.cs)]

Notre méthode AddressAndPayment POST suivront le même modèle que nous avons utilisés dans le StoreManagerController : il essaiera d’accepter l’envoi du formulaire et terminer la commande et ré-affiche le formulaire en cas d’échec.

Une fois la validation de l’entrée du formulaire répond à nos exigences de validation d’une commande, nous allons vérifier la valeur du formulaire du PromoCode directement. En supposant que tout est correct, que nous allons enregistrer les informations mises à jour avec l’ordre, indiquer à l’objet ShoppingCart pour terminer le processus de commande et rediriger vers l’action terminée.

[!code-csharp[Main](mvc-music-store-part-9/samples/sample7.cs)]

En cas de réussite du processus d’extraction, les utilisateurs s’affichera à l’action de contrôleur complète. Cette action effectue une vérification simple pour valider que la commande appartient bien à l’utilisateur connecté avant d’afficher le numéro de commande comme une confirmation.

[!code-csharp[Main](mvc-music-store-part-9/samples/sample8.cs)]

*Remarque : La vue erreur créée automatiquement pour nous dans le dossier /Views/Shared lorsque nous avons commencé le projet.*

Le code CheckoutController complet est comme suit :

[!code-csharp[Main](mvc-music-store-part-9/samples/sample9.cs)]

## <a name="adding-the-addressandpayment-view"></a>Ajout de la vue AddressAndPayment

Maintenant, nous allons créer la vue AddressAndPayment. Avec le bouton droit sur l’un des actions de contrôleur AddressAndPayment et ajouter une vue nommée AddressAndPayment qui est fortement typé comme une commande et utilise le modèle de modification, comme indiqué ci-dessous.

![](mvc-music-store-part-9/_static/image6.png)

Cette vue fera utilise deux techniques que nous avons examiné pendant la génération de la vue StoreManagerEdit :

- Nous allons utiliser Html.EditorForModel() pour afficher les champs de formulaire pour le modèle de commande
- Nous reposera sur les règles de validation à l’aide d’une classe de commande avec les attributs de validation

Nous allons commencer en mettant à jour le code du formulaire pour utiliser Html.EditorForModel(), suivi d’une zone de texte supplémentaire pour le Code de promotion. Le code complet pour la vue AddressAndPayment est indiqué ci-dessous.

[!code-cshtml[Main](mvc-music-store-part-9/samples/sample10.cshtml)]

## <a name="defining-validation-rules-for-the-order"></a>Définition des règles de validation de la commande

Maintenant que notre vue est définie, nous allons définir des règles de validation de notre modèle de commande comme nous l’avons fait précédemment pour le modèle de l’Album. Avec le bouton droit sur le dossier de modèles et ajoutez une classe nommée ordre. Outre les attributs de validation que nous avons utilisé précédemment pour l’Album, nous sera également utiliser une Expression régulière pour valider l’adresse de messagerie de l’utilisateur.

[!code-csharp[Main](mvc-music-store-part-9/samples/sample11.cs)]

Essayez d’envoyer le formulaire avec manquant ou informations non valides maintenant message d’erreur apparaît à l’aide de la validation côté client.

![](mvc-music-store-part-9/_static/image7.png)

OK, nous avons fait plus partie du travail pour le processus de validation ; Nous avons simplement quelques extrémités d’et probabilités pour terminer. Nous devons ajouter deux vues simples, et nous avons besoin prendre en charge de la procédure de transfert des informations d’achat pendant le processus de connexion.

## <a name="adding-the-checkout-complete-view"></a>Ajout de la vue complète de l’extraction

La vue complète de l’extraction est assez simple, car il a juste besoin d’afficher l’ID de commande. Avec le bouton droit sur l’action de contrôleur complète et ajouter une vue nommée complète qui est fortement typé comme un entier.

![](mvc-music-store-part-9/_static/image8.png)

Maintenant nous mettrons à jour le code de la vue pour afficher l’ID de commande, comme indiqué ci-dessous.

[!code-cshtml[Main](mvc-music-store-part-9/samples/sample12.cshtml)]

## <a name="updating-the-error-view"></a>Mise à jour de la vue de l’erreur

Le modèle par défaut inclut une vue de l’erreur dans le dossier vues partagé afin qu’il puisse être réutilisé ailleurs dans le site. Cet affichage de l’erreur contient une erreur très simple et n’utilise pas notre site mise en page, donc nous allons mettre à jour.

Dans la mesure où il s’agit d’une page d’erreur générique, le contenu est très simple. Nous allons inclure un message et un lien pour accéder à la page précédente dans l’historique si l’utilisateur souhaite retenter leur action.

[!code-cshtml[Main](mvc-music-store-part-9/samples/sample13.cshtml)]


>[!div class="step-by-step"]
[Précédent](mvc-music-store-part-8.md)
[Suivant](mvc-music-store-part-10.md)
