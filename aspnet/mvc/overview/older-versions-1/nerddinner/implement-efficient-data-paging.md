---
uid: mvc/overview/older-versions-1/nerddinner/implement-efficient-data-paging
title: "Implémenter la pagination des données efficace | Documents Microsoft"
author: microsoft
description: "Étape 8 montre comment ajouter des prise en charge la pagination à nos URL /Dinners afin qu’au lieu d’afficher 1000 préparés à la fois, nous affichons uniquement 10 préparés à venir à..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: adea836d-dbc2-4005-94ea-53aef09e9e34
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/implement-efficient-data-paging
msc.type: authoredcontent
ms.openlocfilehash: 0b0fba604f97d3bb72d2d403e643b422b9ce48bd
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="implement-efficient-data-paging"></a>Implémenter la pagination des données efficace
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> Il s’agit d’étape 8 a gratuit [« « l’application NerdDinner](introducing-the-nerddinner-tutorial.md) qui parcours à comment générer un petit, mais se termine, application web à l’aide d’ASP.NET MVC 1.
> 
> Étape 8 montre comment ajouter la prise en charge la pagination à nos URL /Dinners afin qu’au lieu d’afficher 1000 préparés à la fois, nous allons uniquement afficher 10 préparés à venir à la fois - et permettent aux utilisateurs finaux revenir à la page et de reculer dans la liste entière d’une manière conviviale de moteurs de recherche.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-step-8-paging-support"></a>De NerdDinner étape 8 : Prise en charge de la pagination

Si notre site réussit, elle aura des milliers de préparés à venir. Nous devons vous assurer que notre interface utilisateur met à l’échelle pour gérer tous ces préparés et permet aux utilisateurs de parcourir les. Pour ce faire, nous allons ajouter la prise en charge de la pagination à notre */Dinners* URL afin qu’au lieu de l’affichage de 1000 préparés à la fois, nous allons uniquement afficher 10 préparés à venir à la fois - et permettent aux utilisateurs finaux à la page principale et de reculer dans l’intégralité de la liste dans une manière conviviale de moteurs de recherche.

### <a name="index-action-method-recap"></a>Récapitulatif de méthode d’Action index()

La méthode d’action Index() au sein de notre classe DinnersController actuellement semble ci-dessous :

[!code-csharp[Main](implement-efficient-data-paging/samples/sample1.cs)]

Lorsqu’une demande est faite pour le */Dinners* URL, il récupère une liste de toutes les prochaines préparés et restitue ensuite une liste de toutes les :

![](implement-efficient-data-paging/_static/image1.png)

### <a name="understanding-iquerablelttgt"></a>Présentation des IQuerable&lt;T&gt;

*IQueryable&lt;T&gt;*  est une interface qui a été introduite avec LINQ dans le cadre de .NET 3.5. Il permet des scénarios de puissantes « exécution différée » que nous pouvons en tirer parti pour implémenter la prise en charge la pagination.

Dans notre DinnerRepository nous allons retourner IQueryable&lt;Dinner&gt; séquence à partir de notre méthode FindUpcomingDinners() :

[!code-csharp[Main](implement-efficient-data-paging/samples/sample2.cs)]

IQueryable&lt;Dinner&gt; objet retourné par la méthode FindUpcomingDinners() encapsule une requête pour récupérer des objets de dîner de notre base de données à l’aide de LINQ to SQL. Important, il ne sera pas exécuter la requête sur la base de données jusqu'à ce que la tentative d’accès/itérer sur les données dans la requête, ou jusqu'à ce que nous appeler la méthode ToList() sur celle-ci. Le code d’appel de méthode de notre FindUpcomingDinners() peut choisir Ajouter des opérations/filtres « chaînées » supplémentaires au IQueryable&lt;Dinner&gt; objet avant d’exécuter la requête. LINQ to SQL, puis est assez intelligente pour exécuter la requête combinée sur la base de données lorsque les données sont demandées.

Pour implémenter la logique de pagination nous pouvons mettre à jour méthode d’action de notre DinnersController Index() afin qu’elle s’applique à des opérateurs de « Ignorer » et « Take » supplémentaires au IQueryable retourné&lt;Dinner&gt; séquence avant l’appel de ToList() sur celui-ci :

[!code-csharp[Main](implement-efficient-data-paging/samples/sample3.cs)]

Le code ci-dessus ignore les 10 premiers préparés à venir dans la base de données, puis renvoie 20 préparés. LINQ to SQL est assez intelligente pour construire une requête optimisée de SQL qui exécute cette non-exécution de logique dans la base de données SQL et non dans le serveur web. Cela signifie que même si nous avons des millions de préparés à venir dans la base de données, seules les 10 que nous souhaitons sera récupéré dans le cadre de cette demande (rendant efficace et évolutive).

### <a name="adding-a-page-value-to-the-url"></a>Ajout d’une valeur de « page » à l’URL

Au lieu de coder en dur une plage de pages spécifique, vous voudrez que nos URL pour inclure un paramètre de « page » qui indique la plage Dinner demande d’un utilisateur.

#### <a name="using-a-querystring-value"></a>À l’aide d’une valeur de chaîne de requête

Le code ci-dessous montre comment nous pouvons mettre à jour notre méthode d’action Index() pour prendre en charge un paramètre de chaîne de requête et activer des URL telles que */Dinners ? page = 2*:

[!code-csharp[Main](implement-efficient-data-paging/samples/sample4.cs)]

La méthode d’action Index() ci-dessus a un paramètre nommé « page ». Le paramètre est déclaré en tant qu’entier nullable (qui est le type int ? indique). Cela signifie que la */Dinners ? page = 2* URL entraîne une valeur de « 2 » pour être passée en tant que la valeur du paramètre. Le */Dinners* URL (sans valeur querystring) entraîne une valeur null à passer.

Nous sommes en multipliant la valeur de la page par la taille de page (dans ce cas 10 lignes) pour déterminer combien préparés à ignorer. Nous utilisons le [C# » « opérateur de fusion null ( ?) ](https://weblogs.asp.net/scottgu/archive/2007/09/20/the-new-c-null-coalescing-operator-and-using-it-with-linq.aspx) ce qui est utile lorsque vous traitez des types nullable. Le code ci-dessus assigne page à la valeur 0 si le paramètre de page est null.

#### <a name="using-embedded-url-values"></a>À l’aide de valeurs d’URL incorporée

Une alternative à l’aide d’une valeur de chaîne de requête serait pour incorporer le paramètre de page dans l’URL réelle proprement dite. Par exemple : */Dinners/Page/2* ou *préparés/2*. ASP.NET MVC inclut un puissant moteur de routage d’URL qui facilite la prise en charge des scénarios comme celui-ci.

Nous pouvons enregistrer les règles de routage personnalisées qui correspondent à n’importe quel URL ou les URL entrantes format à toute méthode de classe ou une action contrôleur que nous voulons. Il suffit d’action consiste à ouvrir le fichier Global.asax dans notre projet :

![](implement-efficient-data-paging/_static/image2.png)

Puis enregistrez une nouvelle règle de mappage à l’aide de la méthode d’assistance MapRoute() comme le premier appel à des itinéraires. MapRoute() ci-dessous :

[!code-csharp[Main](implement-efficient-data-paging/samples/sample5.cs)]

Ci-dessus, nous enregistrons une nouvelle règle de routage nommée « UpcomingDinners ». Il a le format d’URL, nous indiquons « préparés/Page / {page} », où {page} est une valeur de paramètre incorporée dans l’URL. Le troisième paramètre à la méthode MapRoute() indique que nous devons mapper des URL qui correspondent à ce format pour la méthode d’action Index() sur la classe DinnersController.

Nous pouvons utiliser le même Index() code que nous avons dû avant avec notre scénario de chaîne de requête – sauf maintenant notre paramètre « page » est issu de l’URL et pas la chaîne de requête :

[!code-csharp[Main](implement-efficient-data-paging/samples/sample6.cs)]

Et maintenant lorsque nous, exécutez l’application et tapez */Dinners* nous verrons les 10 premiers préparés à venir :

![](implement-efficient-data-paging/_static/image3.png)

Et lorsque vous tapez dans */Dinners/Page/1* nous allons voir la page suivante de préparés :

![](implement-efficient-data-paging/_static/image4.png)

### <a name="adding-page-navigation-ui"></a>Ajout d’interface utilisateur de navigation de page

La dernière étape pour terminer notre scénario d’échange seront pour implémenter des « suivant » et « précédent » IU de navigation au sein de notre modèle d’affichage pour permettre aux utilisateurs d’ignorer les données Dinner facilement.

Pour implémenter correctement, nous devez connaître le nombre total de préparés dans la base de données, ainsi que la manière dont plusieurs pages de données que cela se traduit par. Ensuite, nous devrons déterminer si la valeur actuellement demandée « page » est au début ou à la fin des données et afficher ou masquer l’interface utilisateur de « précédent » et « suivant » en conséquence. Nous avons implémenter cette logique dans notre méthode d’action Index(). Nous pouvons également ajouter une classe d’assistance à notre projet qui encapsule cette logique d’une manière plus réutilisable.

Voici une classe d’assistance « PaginatedList » simple qui dérive de la liste&lt;T&gt; classe de collection intégrée à .NET Framework. Il implémente une classe de collection réutilisable qui peut être utilisée pour paginer n’importe quelle séquence de données de IQueryable. Dans notre application NerdDinner nous aurons il fonctionne sur un IQueryable&lt;Dinner&gt; résultats, mais il pourrait tout aussi facilement être utilisé contre IQueryable&lt;produit&gt; ou IQueryable&lt;client&gt;des résultats dans d’autres scénarios d’application :

[!code-csharp[Main](implement-efficient-data-paging/samples/sample7.cs)]

Notez au-dessus de la manière dont il calcule et puis expose des propriétés telles que « PageIndex », « PageSize », « TotalCount » et « TotalPages ». Il expose également deux propriétés de l’assistance « HasPreviousPage » et « HasNextPage » qui indiquent si la page de données dans la collection est au début ou à la fin de la séquence d’origine. Le code ci-dessus génère deux requêtes SQL à exécuter : la première pour récupérer le nombre du nombre total d’objets du dîner (cela ne retourne pas les objets – au lieu de cela, il exécute une instruction « Sélectionnez nombre » qui retourne un entier) et le deuxième pour récupérer uniquement les lignes de données que nous avons besoin de notre base de données pour la page de données actuelle.

Nous pouvons puis mettre à jour notre méthode d’assistance de DinnersController.Index() pour créer un PaginatedList&lt;Dinner&gt; à partir de notre DinnerRepository.FindUpcomingDinners() résultat et le passer à notre modèle de vue :

[!code-csharp[Main](implement-efficient-data-paging/samples/sample8.cs)]

Nous pouvons puis mettre à jour le modèle d’affichage \Views\Dinners\Index.aspx hériter ViewPage&lt;NerdDinner.Helpers.PaginatedList&lt;Dinner&gt; &gt; au lieu de ViewPage&lt;IEnumerable&lt;Dinner&gt;&gt;, puis ajoutez le code suivant au bas de notre modèle d’affichage pour afficher ou masquer l’interface utilisateur de navigation précédent et suivant :

[!code-aspx[Main](implement-efficient-data-paging/samples/sample9.aspx)]

Notez que ci-dessus comment nous utilisons la méthode d’assistance Html.RouteLink() générer notre des liens hypertexte. Cette méthode est similaire à la méthode d’assistance Html.ActionLink(), nous avons utilisé précédemment. La différence est que nous allons générer l’URL à l’aide de la règle que nous préparons dans notre fichier Global.asax de routage « UpcomingDinners ». Cela garantit que nous allons générer des URL à notre méthode d’action Index() qui ont le format : */Dinners/Page / {page}* – où la valeur {page} est une variable que nous fournissons ci-dessus selon le PageIndex actuel.

Et maintenant si nous exécutons notre application nouveau nous verrons 10 préparés à la fois dans le navigateur :

![](implement-efficient-data-paging/_static/image5.png)

Nous avons également &lt; &lt; &lt; et &gt; &gt; &gt; interface utilisateur en bas de la page qui permet d’ignorer avant et vers l’arrière sur nos données à l’aide de rechercher les URL accessibles de moteur de navigation :

![](implement-efficient-data-paging/_static/image6.png)

| **Rubrique de côté : Comprendre les implications de IQueryable&lt;T&gt;** |
| --- |
| IQueryable&lt;T&gt; est une fonctionnalité très puissante qui permet à une variété de scénarios intéressant de l’exécution différée (telles que la pagination et composition en fonction des requêtes). Comme avec toutes les autres fonctionnalités performantes, vous souhaitez être prudent avec l’utilisation et assurez-vous qu’il n’est pas détourné. Il est important de noter que IQueryable retournant&lt;T&gt; résultat à partir de votre référentiel permet au code appelant ajouter sur les méthodes d’opérateur chaînées à ce dernier et donc participer à l’exécution de requête finale. Si vous ne souhaitez pas fournir de code appelant cette possibilité, vous devez retourner sauvegarder IList&lt;T&gt; ou IEnumerable&lt;T&gt; résultats - qui contiennent les résultats d’une requête qui a déjà été exécuté. Pour les scénarios de pagination, cela nécessiterait afin de transmettre la logique de la pagination des données réelles dans la méthode de référentiel appelée. Dans ce scénario nous pouvons mettre à jour notre méthode de recherche FindUpcomingDinners() pour avoir une signature que soit retourné une PaginatedList : PaginatedList&lt; Dinner&gt; {} de FindUpcomingDinners (pageIndex d’int, int pageSize) ou retour arrière IList &lt;Dinner&gt;et « totalCount » out param pour renvoyer le nombre total de préparés : IList&lt;Dinner&gt; FindUpcomingDinners (int pageIndex, pageSize int, out int totalCount) {} |

### <a name="next-step"></a>Étape suivante

Nous allons maintenant voir comment nous pouvons ajouter la prise en charge de l’authentification et autorisation à notre application.

>[!div class="step-by-step"]
[Précédent](re-use-ui-using-master-pages-and-partials.md)
[Suivant](secure-applications-using-authentication-and-authorization.md)
