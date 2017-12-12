---
uid: web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-5
title: "Partie 5 : La logique métier | Documents Microsoft"
author: JoeStagner
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 5 ajoute une logique métier."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/21/2010
ms.topic: article
ms.assetid: eaef475a-ca91-47ea-a4a7-d074005ed80c
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-5
msc.type: authoredcontent
ms.openlocfilehash: e205788e05a2ad94d86d4847c11c40898b1c3113
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-5-business-logic"></a>Partie 5 : La logique métier
====================
par [Joe Stagner](https://github.com/JoeStagner)

> Tailspin Spyworks montre comment extrêmement simple est de créer des applications puissantes et évolutives pour la plateforme .NET. Il illustre comment utiliser les nouvelles fonctionnalités dans ASP.NET 4 pour créer un magasin en ligne, y compris les achats, l’extraction et l’administration.
> 
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 5 ajoute une logique métier.


## <a id="_Toc260221671"></a>Ajout d’une logique métier

Nous voulons que notre expérience d’achat chaque fois que quelqu'un visite notre site web. Visiteurs sera en mesure de parcourir et ajouter des éléments au panier d’achat même s’ils ne sont pas inscrits ou connectés. Lorsqu’elles sont prêtes à extraire ils reçoivent la possibilité de s’authentifier et si elles ne sont pas encore membres qu’ils seront en mesure de créer un compte.

Cela signifie que nous devons implémenter la logique permettant de convertir le panier d’achat d’un état anonyme dans un état « Utilisateur inscrit ».

Nous allons créer un répertoire nommé « Classes » avec le bouton droit sur le dossier puis créez un nouveau fichier « Classe » MyShoppingCart.cs

![](tailspin-spyworks-part-5/_static/image1.jpg)

![](tailspin-spyworks-part-5/_static/image1.png)

Comme mentionné précédemment nous sera extension de la classe qui implémente la page MyShoppingCart.aspx et nous fera à l’aide. Construction de puissantes « classe partielle » du réseau.

Voici à quoi ressemble l’appel généré pour notre fichier MyShoppingCart.aspx.cf.

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample1.cs)]

Notez l’utilisation du mot clé « partielle ».

Le fichier de classe qui nous vient d’être générées ressemble à ceci.

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample2.cs)]

Fusionner nos mises en œuvre en ajoutant le mot clé partiels à ce fichier.

Notre nouveau fichier de classe ressemble à ceci.

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample3.cs)]

La première méthode que vous allez ajouter à la classe est la méthode « AddItem ». Il s’agit de la méthode qui sera finalement appelée lorsque l’utilisateur clique sur les liens « Ajouter à l’Art » sur les pages de la liste des produits et des détails sur le produit.

Ajoutez le code suivant à en utilisant les instructions en haut de la page.

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample4.cs)]

Et ajouter cette méthode à la classe MyShoppingCart.

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample5.cs)]

Nous utilisons LINQ to Entities pour voir si l’élément est déjà dans le panier d’achat. Si, par conséquent, nous mettre à jour la quantité commandée de l’élément, sinon nous créer une nouvelle entrée pour l’élément sélectionné

Pour pouvoir appeler cette méthode, nous implémenterons une page AddToCart.aspx qui non seulement cette méthode de classe, mais affiche ensuite un panier = après l’ajout de l’élément actuel.

Avec le bouton droit sur le nom de la solution dans l’Explorateur de solutions et ajoutez et nouvelle page nommée AddToCart.aspx comme nous l’avons fait précédemment.

Pendant que nous pourrions utiliser cette page pour afficher les résultats intermédiaires telles que des problèmes de stock faibles, etc., dans notre implémentation, la page ne sont pas effectuer le rendu, mais plutôt appeler la logique de « Add » et rediriger.

Pour ce faire, nous ajouterons le code suivant à la Page\_événement de chargement.

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample6.cs)]

Notez que nous récupérons le produit à ajouter au panier d’achat à partir d’un paramètre de chaîne de requête et en appelant la méthode AddItem de notre classe.

En supposant qu’aucune erreur ne se produisent lorsque le contrôle est passé à la page SHoppingCart.aspx qui nous implémenterons entièrement ensuite. S’il doit y avoir une erreur nous lève une exception.

Actuellement nous n'avons pas encore implémenté un gestionnaire d’erreurs globales pour cette exception va passer non prise en charge par notre application, mais nous sera bientôt remédier.

Notez également l’utilisation de l’instruction Debug.Fail() (disponible via`using System.Diagnostics;)`

Est de l’application s’exécute dans le débogueur, cette méthode affiche une boîte de dialogue détaillée avec des informations sur l’état des applications, ainsi que le message d’erreur qui nous spécifions.

Lors de l’exécution en production la Debug.Fail() instruction est ignorée.

Vous noterez dans le code situé au-dessus d’un appel à une méthode dans nos noms de classe de panier d’achat « GetShoppingCartId ».

Ajoutez le code pour implémenter la méthode comme suit.

Notez que nous avons également ajouté une mise à jour et l’extraction des boutons et une étiquette où nous pouvons afficher le panier d’achat « total ».

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample7.cs)]

Nous pouvons maintenant ajouter des éléments à notre panier d’achat, mais nous n’avons pas implémenté la logique permettant d’afficher le panier après l’ajout d’un produit.

Par conséquent, dans la page MyShoppingCart.aspx nous allons ajouter un contrôle EntityDataSource et un contrôle GridVire comme suit.

[!code-aspx[Main](tailspin-spyworks-part-5/samples/sample8.aspx)]

Appelez le formulaire dans le concepteur afin que vous pouvez cliquer sur le bouton de mise à jour le panier et générer le Gestionnaire d’événements click qui est spécifié dans la déclaration dans le balisage.

Nous allons implémenter les détails ultérieurement, mais cela nous générez et exécutez notre application sans erreurs.

Lorsque vous exécutez l’application et que vous ajoutez un élément dans le panier d’achat vous voyez ceci.

![](tailspin-spyworks-part-5/_static/image2.jpg)

Notez que nous avons sorties à partir de l’affichage de grille « default » en implémentant les trois colonnes personnalisées.

Le premier est un champ « Lié » pour la quantité modifiable :

[!code-aspx[Main](tailspin-spyworks-part-5/samples/sample9.aspx)]

La prochaine est une colonne « calculée » qui affiche l’élément de ligne de total (l’élément de coût fois la quantité à commander) :

[!code-aspx[Main](tailspin-spyworks-part-5/samples/sample10.aspx)]

Enfin, nous avons une colonne personnalisée qui contient un contrôle de case à cocher l’utilisateur utilisera pour indiquer que l’élément doit être supprimé du plan d’achat.

[!code-aspx[Main](tailspin-spyworks-part-5/samples/sample11.aspx)]

![](tailspin-spyworks-part-5/_static/image3.jpg)

Comme vous pouvez le voir, l’ordre de Total de la ligne est vide, nous allons donc ajouter une logique pour calculer le Total des commandes.

Nous allons tout d’abord implémenter une méthode « GetTotal » à la classe MyShoppingCart.

Dans le fichier MyShoppingCart.cs, ajoutez le code suivant.

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample12.cs)]

Ensuite, dans la Page\_Gestionnaire d’événements Load nous allons peut appeler la méthode GetTotal. En même temps, nous allons ajouter un test pour voir si le panier d’achat est vide et ajustez l’affichage en conséquence, s’il s’agit.

Si le panier d’achat est vide nous maintenant cela :

![](tailspin-spyworks-part-5/_static/image4.jpg)

Et dans le cas contraire, le total.

![](tailspin-spyworks-part-5/_static/image5.jpg)

Toutefois, cette page n’est pas encore terminée.

Nous devons logique supplémentaire pour recalculer le panier d’achat en supprimant les éléments marqués pour suppression et en déterminant les nouvelles valeurs de quantité certaines peuvent avoir été modifiée dans la grille par l’utilisateur.

Permet d’ajouter une méthode « RemoveItem » à notre classe de panier d’achat dans MyShoppingCart.cs pour gérer le cas lorsqu’un utilisateur marque un élément pour la suppression.

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample13.cs)]

Maintenant nous allons ad une méthode pour gérer le cas lorsqu’un utilisateur modifie simplement la qualité pour être classés dans le GridView.

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample14.cs)]

Avec les fonctionnalités de base de mise à jour et suppression en place, nous pouvons implémenter la logique qui met à jour le panier d’achat dans la base de données. (Dans MyShoppingCart.cs)

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample15.cs)]

Vous remarquerez que cette méthode attend deux paramètres. Un est l’Id de panier d’achat et l’autre est un tableau d’objets de type de défini par l’utilisateur.

Pour minimiser la dépendance de notre logique sur les caractéristiques d’interface utilisateur, nous avons défini une structure de données que nous pouvons utiliser pour passer les éléments de panier d’achat à notre code sans avoir à accéder directement le contrôle GridView notre méthode de.

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample16.cs)]

Dans notre fichier MyShoppingCart.aspx.cs nous pouvons utiliser cette structure dans notre gestionnaire d’événements de cliquez sur bouton mise à jour comme suit. Notez qu’en plus de la mise à jour le panier nous mettrons à jour le total de l’achat.

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample17.cs)]

Notez, avec un intérêt particulier, cette ligne de code :

[!code-javascript[Main](tailspin-spyworks-part-5/samples/sample18.js)]

GetValues() est une fonction d’assistance spéciales qui nous implémenterons dans MyShoppingCart.aspx.cs comme suit.

[!code-csharp[Main](tailspin-spyworks-part-5/samples/sample19.cs)]

Cela fournit un moyen adéquat pour accéder aux valeurs des éléments liés dans notre contrôle GridView. Étant donné que notre contrôle de case à cocher « Supprimer l’élément » n’est pas lié nous allons y accéder via la méthode FindControl().

À ce stade du développement de votre projet, nous recevons prêts à mettre en œuvre le processus de validation.

Avant cela nous allons utiliser Visual Studio pour générer la base de données d’appartenance et ajouter un utilisateur dans le référentiel d’appartenance.

>[!div class="step-by-step"]
[Précédent](tailspin-spyworks-part-4.md)
[Suivant](tailspin-spyworks-part-6.md)
