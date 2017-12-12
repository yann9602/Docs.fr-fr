---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/deleting-data
title: "Présentation des Pages Web ASP.NET - suppression des données de la base de données | Documents Microsoft"
author: tfitzmac
description: "Ce didacticiel vous montre comment supprimer une entrée de la base de données individuels. Il suppose que vous avez terminé la série grâce à la mise à jour des données de base de données dans ASP.NET Web PA..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/28/2015
ms.topic: article
ms.assetid: 75b5c1cf-84bd-434f-8a86-85c568eb5b09
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/deleting-data
msc.type: authoredcontent
ms.openlocfilehash: aef31b6170cc3bba2421eb8c2c41e83aadc129c5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="introducing-aspnet-web-pages---deleting-database-data"></a>Présentation des Pages Web ASP.NET - suppression des données de la base de données
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Ce didacticiel vous montre comment supprimer une entrée de la base de données individuels. Il suppose que vous avez terminé la série via [mise à jour de base de données dans les Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=251583).
> 
> Ce que vous allez apprendre :
> 
> - Comment sélectionner un enregistrement individuel à partir d’une liste d’enregistrements.
> - Comment supprimer un enregistrement unique d’une base de données.
> - Comment vérifier que l’utilisateur a cliqué sur un bouton spécifique dans un formulaire.
>   
> 
> Fonctionnalités technologies abordées :
> 
> - Le `WebGrid` helper.
> - L’instruction SQL `Delete` commande.
> - Le `Database.Execute` méthode à exécuter SQL `Delete` commande.


## <a name="what-youll-build"></a>Ce que vous allez générer

Dans le didacticiel précédent, vous avez appris comment mettre à jour un enregistrement de base de données existant. Ce didacticiel est similaire, mais au lieu de la mise à jour de l’enregistrement, vous devez le supprimer. Les processus sont très similaire, sauf que la suppression est plus simple, donc, ce didacticiel est court.

Dans le *films* page, vous allez mettre à jour le `WebGrid` helper afin qu’elle affiche un **supprimer** lien en regard de chaque séquence pour accompagner le **modifier** lien que vous avez ajoutée précédemment.

![Page de films affichant un lien Supprimer pour chaque vidéo](deleting-data/_static/image1.png)

Comme avec la modification, lorsque vous cliquez sur le **supprimer** lien, il vous redirige vers une autre page, où les informations de film sont déjà dans un formulaire :

![Supprimer la page de film avec un film affiché](deleting-data/_static/image2.png)

Vous pouvez ensuite cliquer sur le bouton pour supprimer définitivement un enregistrement.

## <a name="adding-a-delete-link-to-the-movie-listing"></a>Ajout d’un lien de suppression à la liste de films

Vous allez commencer par ajouter un **supprimer** créer un lien vers le `WebGrid` helper. Ce lien est similaire à la **modifier** lien que vous avez ajouté à un didacticiel précédent.

Ouvrez le *Movies.cshtml* fichier.

Modifier la `WebGrid` balisage dans le corps de la page en ajoutant une colonne. Voici le balisage modifié :

[!code-html[Main](deleting-data/samples/sample1.html?highlight=9-10)]

La nouvelle colonne est celle-ci :

[!code-html[Main](deleting-data/samples/sample2.html)]

La manière de configurer la grille, le **modifier** colonne est plus à gauche dans la grille et la **supprimer** colonne est la plus à droite. (Il existe une virgule après la `Year` colonne maintenant, au cas où vous n’avez pas remarquer que.) Rien de spécial sur lequel ces colonnes lien go, et vous pouvez aussi facilement les placer en regard de l’autre. Dans ce cas, ils sont distincts pour les rendre plus difficile à se mélanger.

![Page de films avec des liens d’édition et les détails marquée pour montrer qu’ils ne sont pas adjacents](deleting-data/_static/image3.png)

La nouvelle colonne affiche un lien (`<a>` élément) dont le texte indique « Delete ». La cible du lien (son `href` attribut) est un code qui correspond finalement à quelque chose comme cette URL, avec la `id` valeur différente pour chaque vidéo :

[!code-css[Main](deleting-data/samples/sample3.css)]

Ce lien appelle une page nommée *DeleteMovie* et lui passer l’ID de la séquence que vous avez sélectionné.

Ce didacticiel ne sera pas entrer dans les détails sur la façon dont ce lien est construit, car il est presque identique à la **modifier** lien à partir du didacticiel précédent ([mise à jour de base de données dans ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251583)).

## <a name="creating-the-delete-page"></a>Création de la Page de suppression

Vous pouvez désormais créer la page qui sera la cible pour le **supprimer** liaison dans la grille.

> [!NOTE] 
> 
> **Important** la technique d’abord en sélectionnant un enregistrement à supprimer, puis en utilisant une page distincte et un bouton pour confirmer le processus est extrêmement importante pour la sécurité. Comme vous avez lu dans les didacticiels précédents, ce qui *tout* tri de modification de votre site Web doit *toujours* être effectuée à l’aide d’un formulaire &mdash; , à l’aide d’une opération HTTP POST. Si vous l’avez fait possible de modifier le site suffit de cliquer sur un lien (c'est-à-dire, en utilisant une opération GET), les personnes pourrait faire des demandes simples sur votre site et supprimer vos données. Même un moteur de recherche qui est l’indexation de votre site peut supprimer par inadvertance des données simplement en suivant les liens.
> 
> Lorsque votre application permet de modifier un enregistrement, vous devez présenter l’enregistrement à l’utilisateur pour le modifier quand même. Mais vous pouvez être tenté d’ignorer cette étape pour la suppression d’un enregistrement. Ne passez pas bien que cette étape. (Il est également utile pour les utilisateurs voient l’enregistrement et de confirmer qu’ils vous supprimez l’enregistrement qui ils ont vocation.)
> 
> Dans un jeu de didacticiel ultérieur, vous verrez comment ajouter des fonctionnalités de connexion pour un utilisateur devra se connecter avant de supprimer un enregistrement.


Créer une page nommée *DeleteMovie.cshtml* et remplacez ce qui est dans le fichier avec le balisage suivant :

[!code-cshtml[Main](deleting-data/samples/sample4.cshtml)]

Ce balisage est similaire à la *EditMovie* pages, à ceci près qu’au lieu d’utiliser des zones de texte (`<input type="text">`), le balisage inclut `<span>` éléments. Il n’y a rien ici à modifier. Il vous est afficher les détails des films afin que les utilisateurs peuvent s’assurer qu’ils vous supprimez le film à droite.

Le balisage contient déjà un lien qui permet à l’utilisateur de revenir à la page de liste de films.

Comme dans le *EditMovie* page, l’ID de la séquence sélectionnée est stockée dans un champ masqué. (Il est passé dans la page en premier lieu en tant que valeur de chaîne de requête.) Il existe un `Html.ValidationSummary` appel qui affiche les erreurs de validation. Dans ce cas, l’erreur peut être qu’aucun ID de film a été transmis à la page ou que l’ID de film n’est pas valide. Cette situation peut se produire si une personne a exécuté cette page sans avoir à sélectionner un film dans le *films* page.

La légende du bouton est **supprimer un film**, et son attribut de nom est défini sur `buttonDelete`. Le `name` attribut servira dans le code pour identifier le bouton qui a envoyé le formulaire.

Vous devrez écrire du code pour (1) lire les détails de film lorsque la page s’affiche tout d’abord et (2) réellement supprimer le film lorsque l’utilisateur clique sur le bouton.

## <a name="adding-code-to-read-a-single-movie"></a>Ajout de Code pour lire un film unique

En haut de la *DeleteMovie.cshtml* , ajoutez le bloc de code suivant :

[!code-cshtml[Main](deleting-data/samples/sample5.cshtml)]

Ce balisage est le même que le code correspondant dans le *EditMovie* page. Il obtient l’ID de film hors de la chaîne de requête et utilise l’ID pour lire un enregistrement à partir de la base de données. Le code inclut le test de validation (`IsInt()` et `row != null`) pour vous assurer que l’ID de film qui est passée à la page est valide.

N’oubliez pas que ce code doit exécuter uniquement la première fois que la page s’exécute. Vous ne souhaitez pas relire l’enregistrement vidéo à partir de la base de données lorsque l’utilisateur clique sur le **supprimer un film** bouton. Par conséquent, le code pour lire le film est à l’intérieur d’un test qui indique que `if(!IsPost)` &mdash; , *si la demande n’est pas une opération post (envoi du formulaire)*.

## <a name="adding-code-to-delete-the-selected-movie"></a>Ajout de Code pour supprimer le projet sélectionné

Pour supprimer le film lorsque l’utilisateur clique sur le bouton, ajoutez le code suivant à l’intérieur de l’accolade fermante de la `@` bloc :

[!code-csharp[Main](deleting-data/samples/sample6.cs)]

Ce code est semblable au code de mise à jour un enregistrement existant, mais il est plus simple. Le code exécute essentiellement SQL `Delete` instruction.

 Comme dans le *EditMovie* page, le code est dans un `if(IsPost)` bloc. Cette fois-ci, le `if()` condition est un peu plus complexe : 

[!code-csharp[Main](deleting-data/samples/sample7.cs)]

Il existe deux conditions ici. La première est que la page est soumise, comme vous l’avez vu avant &mdash; `if(IsPost)`.

La deuxième condition est `!Request["buttonDelete"].IsEmpty()`, ce qui signifie que la demande comporte un objet nommé `buttonDelete`. Certes, il est un moyen indirect de bouton qui a envoyé le formulaire de test. Si un formulaire contient plusieurs boutons d’envoi, uniquement le nom de l’utilisateur a cliqué sur le bouton s’affiche dans la demande. Par conséquent, logiquement, si le nom d’un bouton apparaît dans la demande &mdash; ou comme indiqué dans le code, si ce bouton n’est pas vide &mdash; qui est le bouton qui a envoyé le formulaire.

Le `&&` moyen de l’opérateur « et » (et logique). Par conséquent, l’ensemble `if` condition est en cours...

*Cette demande est une demande post (pas une demande de première)*  
  
 AND  
  
*Le* `buttonDelete` *bouton est le bouton qui a envoyé le formulaire.*

Ce formulaire (en fait, cette page) contient un seul bouton, par conséquent, le test supplémentaire pour `buttonDelete` techniquement n’est pas obligatoire. Cependant, vous êtes sur le point d’effectuer une opération qui va supprimer définitivement les données. Vous souhaitez donc veillez possible que vous effectuez l’opération uniquement lorsque l’utilisateur a explicitement demandé. Par exemple, que vous développée plus loin de cette page et ajouté des autres boutons à celle-ci. Même alors, le code qui supprime le film s’exécutera uniquement si la `buttonDelete` bouton a été utilisé.

Comme dans le *EditMovie* page, que vous obtenez l’ID à partir du champ masqué et puis exécutez la commande SQL. La syntaxe de la `Delete` instruction est :

`DELETE FROM table WHERE ID = value`

Il est essentiel d’inclure la `WHERE` clause et le code. Si vous omettez la clause WHERE, *tous les enregistrements dans la table seront supprimés*. Comme vous l’avez vu, vous passez la valeur d’ID pour la commande SQL à l’aide d’un espace réservé.

## <a name="testing-the-movie-delete-process"></a>Tester le processus de suppression de film

Vous pouvez maintenant tester. Exécutez le *films* page, puis cliquez sur **supprimer** en regard d’un film. Lorsque le *DeleteMovie* page s’affiche, cliquez sur **supprimer un film**.

![Supprimer la page de film avec un bouton Supprimer un film mis en surbrillance](deleting-data/_static/image4.png)

Lorsque vous cliquez sur le bouton, le code supprime les films et retourne à la liste de films. Vous pouvez y rechercher le film supprimé et vérifiez qu’il est supprimé.

## <a name="coming-up-next"></a>Prochaine

Le didacticiel suivant vous montre comment permettre à toutes les pages sur votre site une apparence commune et une mise en page.

## <a name="complete-listing-for-movie-page-updated-with-delete-links"></a>Liste complète pour la Page de film (mis à jour avec les liens de suppression)

[!code-cshtml[Main](deleting-data/samples/sample8.cshtml)]

## <a name="complete-listing-for-deletemovie-page"></a>Liste complète de la Page de DeleteMovie

[!code-cshtml[Main](deleting-data/samples/sample9.cshtml)]

## <a name="additional-resources"></a>Ressources supplémentaires

- [Introduction à la programmation Web ASP.NET à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=202890)
- [Instruction DELETE de SQL](http://www.w3schools.com/sql/sql_delete.asp) sur le site W3Schools

>[!div class="step-by-step"]
[Précédent](updating-data.md)
[Suivant](layouts.md)
