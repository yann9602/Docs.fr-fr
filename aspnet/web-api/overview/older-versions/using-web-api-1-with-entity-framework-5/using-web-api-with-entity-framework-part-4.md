---
uid: web-api/overview/older-versions/using-web-api-1-with-entity-framework-5/using-web-api-with-entity-framework-part-4
title: "Partie 4 : Ajout d’une vue Admin | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/04/2012
ms.topic: article
ms.assetid: 792f4513-a508-4d14-a0dd-1a2fe282c7bb
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/older-versions/using-web-api-1-with-entity-framework-5/using-web-api-with-entity-framework-part-4
msc.type: authoredcontent
ms.openlocfilehash: 2960eee37201655a9e4632bf0196ba18a0e2e82a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-4-adding-an-admin-view"></a>Partie 4 : Ajout d’une vue de l’administrateur
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASP-NET-Web-API-with-afa30545)

## <a name="add-an-admin-view"></a>Ajouter une vue de l’administrateur

Nous allons maintenant nous tourner vers le côté client et ajouter une page qui peut utiliser des données à partir du contrôleur de l’administrateur. La page permettra aux utilisateurs de créer, modifier ou supprimer des produits, en envoyant les requêtes AJAX au contrôleur.

Dans l’Explorateur de solutions, développez le dossier contrôleurs et d’ouvrir le fichier HomeController.cs. Ce fichier contient un contrôleur MVC. Ajoutez une méthode nommée `Admin`:

[!code-csharp[Main](using-web-api-with-entity-framework-part-4/samples/sample1.cs)]

Le **HttpRouteUrl** méthode crée l’URI à l’API web, et nous enregistrez-le dans le sac d’affichage pour une date ultérieure.

Ensuite, placez le curseur de texte dans le `Admin` méthode d’action, puis avec le bouton droit et sélectionnez **ajouter une vue**. Cela affiche la **ajouter une vue** boîte de dialogue.

![](using-web-api-with-entity-framework-part-4/_static/image1.png)

Dans le **ajouter une vue** boîte de dialogue, nom de la vue « Admin ». Activez la case à cocher **créer une vue fortement typée**. Sous **classe de modèle**, sélectionnez « Product (ProductStore.Models) ». Laissez toutes les autres options en tant que leurs valeurs par défaut.

![](using-web-api-with-entity-framework-part-4/_static/image2.png)

En cliquant sur **ajouter** ajoute un fichier nommé Admin.cshtml sous Views/Home. Ouvrez ce fichier et ajouter le code HTML suivant. Ce code HTML définit la structure de la page, mais aucun fonctionnalités ne sont encore associés.

[!code-cshtml[Main](using-web-api-with-entity-framework-part-4/samples/sample2.cshtml)]

## <a name="create-a-link-to-the-admin-page"></a>Créer un lien vers la Page d’administration

Dans l’Explorateur de solutions, développez le dossier Views, puis le dossier Shared. Ouvrez le fichier nommé \_Layout.cshtml. Recherchez le **ul** élément avec l’id = « menu » et un lien d’action pour l’affichage de l’administrateur :

[!code-cshtml[Main](using-web-api-with-entity-framework-part-4/samples/sample3.cshtml)]

> [!NOTE]
> Dans l’exemple de projet, j’ai apporté quelques autres modifications esthétique, par exemple en remplaçant la chaîne « Votre logo ici ». Elles n’affectent pas la fonctionnalité de l’application. Vous pouvez télécharger le projet et comparer les fichiers.


Exécutez l’application et cliquez sur le lien « Admin » qui apparaît en haut de la page d’accueil. La page d’administration doit se présenter comme suit :

![](using-web-api-with-entity-framework-part-4/_static/image3.png)

Droit à présent, la page ne fait rien. Dans la section suivante, nous allons utiliser Knockout.js pour créer une interface utilisateur dynamique.

## <a name="add-authorization"></a>Ajouter une autorisation

La page d’administration est actuellement accessible à toute personne visitant le site. Nous allons modifier cette option pour restreindre l’accès aux administrateurs.

Commencez par ajouter un rôle « Administrateur » et un utilisateur administrateur. Dans l’Explorateur de solutions, développez le dossier de filtres et ouvrir le fichier nommé InitializeSimpleMembershipAttribute.cs. Recherchez le `SimpleMembershipInitializer` constructeur. Après l’appel à **WebSecurity.InitializeDatabaseConnection**, ajoutez le code suivant :

[!code-csharp[Main](using-web-api-with-entity-framework-part-4/samples/sample4.cs)]

Il s’agit d’un moyen simple et rapide pour ajouter le rôle « Administrateur » et créez un utilisateur pour le rôle.

Dans l’Explorateur de solutions, développez le dossier Controllers et ouvrez le fichier HomeController.cs. Ajouter le **Authorize** attribut le `Admin` (méthode).

[!code-csharp[Main](using-web-api-with-entity-framework-part-4/samples/sample5.cs)]

Ouvrez le fichier AdminController.cs et ajoutez le **Authorize** à l’ensemble d’attributs `AdminController` classe.

[!code-csharp[Main](using-web-api-with-entity-framework-part-4/samples/sample6.cs)]

> [!NOTE]
> MVC et les API Web définissent **Authorize** attributs, dans différents espaces de noms. MVC utilise **System.Web.Mvc.AuthorizeAttribute**, tandis que l’API Web utilise **System.Web.Http.AuthorizeAttribute**.


Seuls les administrateurs peuvent désormais afficher la page d’administration. En outre, si vous envoyez une demande HTTP au contrôleur Admin, la demande doit contenir un cookie d’authentification. Si ce n’est pas le cas, le serveur envoie une réponse HTTP 401 (non autorisé). Vous pouvez le voir dans Fiddler en envoyant une demande GET pour `http://localhost:*port*/api/admin`.

>[!div class="step-by-step"]
[Précédent](using-web-api-with-entity-framework-part-3.md)
[Suivant](using-web-api-with-entity-framework-part-5.md)
