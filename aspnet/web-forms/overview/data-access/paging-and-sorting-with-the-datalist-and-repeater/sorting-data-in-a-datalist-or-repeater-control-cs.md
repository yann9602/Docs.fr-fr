---
uid: web-forms/overview/data-access/paging-and-sorting-with-the-datalist-and-repeater/sorting-data-in-a-datalist-or-repeater-control-cs
title: "Tri des données dans un contrôle de répéteur (c#) ou le contrôle DataList | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons examiner comment inclure la prise en charge dans le contrôle DataList et répéteur de tri, ainsi que comment construire un contrôle DataList ou un répéteur dont les données peuvent..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/13/2006
ms.topic: article
ms.assetid: f52c302a-1b7c-46fe-8a13-8412c95cbf6d
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/paging-and-sorting-with-the-datalist-and-repeater/sorting-data-in-a-datalist-or-repeater-control-cs
msc.type: authoredcontent
ms.openlocfilehash: f7ab0df2ebfa24b0928117e683325b158e3aad1c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="sorting-data-in-a-datalist-or-repeater-control-c"></a>Tri des données dans un contrôle DataList ou le contrôle du répéteur (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_45_CS.exe) ou [télécharger le PDF](sorting-data-in-a-datalist-or-repeater-control-cs/_static/datatutorial45cs1.pdf)

> Dans ce didacticiel, nous allons examiner comment inclure la prise en charge dans le contrôle DataList et répéteur de tri, ainsi que comment construire un contrôle DataList ou un répéteur dont les données peuvent être paginées et triées.


## <a name="introduction"></a>Introduction

Dans le [didacticiel précédent](paging-report-data-in-a-datalist-or-repeater-control-cs.md) nous avons examiné comment ajouter la prise en charge la pagination pour un contrôle DataList. Nous avons créé une nouvelle méthode dans le `ProductsBLL` classe (`GetProductsAsPagedDataSource`) qui a retourné un `PagedDataSource` objet. Lorsqu’il est lié à un contrôle DataList ou un répéteur, DataList ou répéteur affiche uniquement la page demandée de données. Cette technique est similaire à ce qui est utilisé en interne par les contrôles GridView, DetailsView et FormView pour fournir la fonctionnalité de pagination de leur valeur par défaut.

En plus de la prise en charge la pagination de l’offre, le contrôle GridView inclut également prêtes à l’emploi de tri de prise en charge. Répéteur ni DataList fournit une fonctionnalité de tri intégrée ; Toutefois, les fonctionnalités de tri peuvent être ajoutée avec un peu de code. Dans ce didacticiel, nous allons examiner comment inclure la prise en charge dans le contrôle DataList et répéteur de tri, ainsi que comment construire un contrôle DataList ou un répéteur dont les données peuvent être paginées et triées.

## <a name="a-review-of-sorting"></a>Une revue de tri

Comme nous l’avons vu dans la [la pagination et le tri des données de rapport](../paging-and-sorting/paging-and-sorting-report-data-cs.md) (didacticiel), le contrôle GridView fournit en dehors de la zone de tri de prise en charge. Chaque champ GridView peut être associé à `SortExpression`, ce qui indique le champ de données en fonction desquelles trier les données. Lorsque le GridView s `AllowSorting` est définie sur `true`, chaque champ de GridView a un `SortExpression` valeur de la propriété son en-tête est rendue sous la forme d’un bouton de lien. Lorsqu’un utilisateur clique sur un en-tête s GridView particulier, une publication (postback) se produit et les données sont triées en fonction du champ où vous avez cliqué s `SortExpression`.

Le contrôle GridView a un `SortExpression` , propriété qui stocke le `SortExpression` du champ GridView, les données sont triées par. En outre, un `SortDirection` propriété indique si les données sont triées dans l’ordre croissant ou décroissant (si un utilisateur clique sur un lien particulier d’en-tête champ s GridView deux fois de suite, l’ordre de tri est activé ou désactivé).

Lorsque le contrôle GridView est lié à son contrôle de source de données, il transmet ses `SortExpression` et `SortDirection` propriétés aux données de contrôle de code source. Le contrôle de source de données récupère les données et les trie en fonction de l’élément `SortExpression` et `SortDirection` propriétés. Après le tri des données, le contrôle de source de données retourne au GridView.

Pour répliquer cette fonctionnalité avec les contrôles DataList ou répéteur, nous devons :

- Créer une interface de tri
- N’oubliez pas le champ de données à trier et s’il faut trier dans l’ordre croissant ou décroissant
- Indiquer l’ObjectDataSource pour trier les données par un champ de données spécifique

Nous allons aborder ces trois tâches dans les étapes 3 et 4. Après cela, nous allons examiner comment inclure à la fois la pagination et le tri de prise en charge dans un contrôle DataList ou un répéteur.

## <a name="step-2-displaying-the-products-in-a-repeater"></a>Étape 2 : Afficher les produits dans un répéteur.

Avant de nous soucier d’implémenter les fonctionnalités associées au tri, permettent de s commencez par répertorier les produits dans un contrôle du répéteur. Commencez par ouvrir le `Sorting.aspx` page dans le `PagingSortingDataListRepeater` dossier. Ajouter un contrôle de répéteur à la page web, en définissant ses `ID` propriété `SortableProducts`. À partir de la balise active de répéteur s, créez un nouveau ObjectDataSource nommé `ProductsDataSource` et configurez-le pour extraire des données à partir de la `ProductsBLL` classe s `GetProducts()` (méthode). Sélectionnez (aucun) option dans la liste déroulante dans les onglets INSERT, UPDATE et DELETE.


[![Créer un ObjectDataSource et le configurer pour utiliser la méthode GetProductsAsPagedDataSource()](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image2.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image1.png)

**Figure 1**: créer un ObjectDataSource et le configurer pour utiliser le `GetProductsAsPagedDataSource()` (méthode) ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image3.png))


[![Définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image5.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image4.png)

**Figure 2**: définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None) ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image6.png))


À la différence avec le contrôle DataList Visual Studio ne crée pas automatiquement un `ItemTemplate` pour le contrôle du répéteur après sa liaison à une source de données. En outre, nous devons ajouter ce `ItemTemplate` de façon déclarative, comme la balise active du contrôle s répéteur ne dispose pas de l’option Modifier les modèles trouvée dans le contrôle DataList s. S permettent d’utiliser le même `ItemTemplate` du didacticiel précédent, qui affiche le nom de produit s, le fournisseur et la catégorie.

Après avoir ajouté le `ItemTemplate`, le balisage déclaratif s répéteur et ObjectDataSource doit ressembler à ce qui suit :


[!code-aspx[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample1.aspx)]

La figure 3 illustre cette page lors de l’affichage via un navigateur.


[![Chaque produit s, nom de fournisseur et la catégorie s’affiche.](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image8.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image7.png)

**Figure 3**: chaque produit s nom, fournisseur et la catégorie est affiché ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image9.png))


## <a name="step-3-instructing-the-objectdatasource-to-sort-the-data"></a>Étape 3 : Demandant ObjectDataSource pour trier les données

Pour trier les données affichées dans le répéteur, nous devons informer ObjectDataSource de l’expression de tri par lequel les données doivent être triées. Avant de ObjectDataSource récupère ses données, il déclenche tout d’abord son [ `Selecting` événement](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.objectdatasource.selecting.aspx), qui fournit une opportunité pour spécifier une expression de tri. Le `Selecting` un objet de type est passé au gestionnaire d’événements [ `ObjectDataSourceSelectingEventArgs` ](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.objectdatasourceselectingeventargs.aspx), qui a une propriété nommée [ `Arguments` ](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.objectdatasourceselectingeventargs.arguments.aspx) de type [ `DataSourceSelectArguments` ](https://msdn.microsoft.com/en-us/library/system.web.ui.datasourceselectarguments.aspx). Le `DataSourceSelectArguments` classe est conçu pour transmettre les demandes liées aux données à partir d’un consommateur de données au contrôle de source de données et inclut un [ `SortExpression` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.datasourceselectarguments.sortexpression.aspx).

Pour passer les informations de tri à partir de la page ASP.NET pour ObjectDataSource, créez un gestionnaire d’événements pour le `Selecting` événement et utilisez le code suivant :


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample2.cs)]

Le *sortExpression* valeur doit être affectée le nom du champ de données pour trier des données (par exemple, ProductName). Il n’existe aucune propriété liés à la direction de tri, par conséquent, si vous souhaitez trier les données dans l’ordre décroissant, ajoutez la chaîne de description à la *sortExpression* valeur (par exemple, ProductName DESC).

Pour commencer, essayez certaines différentes valeurs codées en dur pour *sortExpression* et les résultats des tests dans un navigateur. Comme le montre la Figure 4, lorsque vous utilisez DESC ProductName comme le *sortExpression*, les produits sont triés par leur nom dans l’ordre alphabétique inverse.


[![Les produits sont triés par leur nom dans l’ordre alphabétique inverse](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image11.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image10.png)

**Figure 4**: les produits sont triés par leur nom dans l’ordre alphabétique inverse ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image12.png))


## <a name="step-4-creating-the-sorting-interface-and-remembering-the-sort-expression-and-direction"></a>Étape 4 : Création de l’Interface de tri et la mémorisation de l’Expression de tri et la Direction

Activation sur le tri de la prise en charge dans le GridView convertit chaque texte d’en-tête de champ triable s un LinkButton qui, lorsque vous cliquez dessus, trie les données en conséquence. Une interface de ce tri de sens pour le GridView, où ses données sont bien organisées en colonnes. Pour les contrôles DataList et répéteur, toutefois, une autre interface de tri n’est nécessaire. Une interface commune de tri pour une liste de données (par opposition à une grille de données) est une liste déroulante, qui fournit les champs par lequel les données peuvent être triées. Permettent d’implémenter une telle interface pour ce didacticiel s.

Ajouter un contrôle DropDownList Web ci-dessus le `SortableProducts` répéteur et définissez son `ID` propriété `SortBy`. À partir de la fenêtre Propriétés, cliquez sur le bouton de sélection dans le `Items` propriété pour afficher l’éditeur de collections ListItem. Ajouter `ListItem` s pour trier les données par le `ProductName`, `CategoryName`, et `SupplierName` champs. Ajoutez également une `ListItem` pour trier les produits par leur nom dans l’ordre alphabétique inverse.

Le `ListItem` `Text` propriétés peuvent être définies à n’importe quelle valeur (par exemple, nom), mais la `Value` propriétés doivent être définies sur le nom du champ de données (par exemple, ProductName). Pour trier les résultats par ordre décroissant, ajoutez la chaîne de description pour le nom de champ de données, comme ProductName DESC.


![Ajouter un élément ListItem pour chacun des champs de données pouvant être trié](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image13.png)

**Figure 5**: ajouter un `ListItem` pour chacun des champs de données pouvant être trié


Enfin, ajoutez un contrôle bouton Web vers la droite de DropDownList. Définir son `ID` à `RefreshRepeater` et son `Text` propriété de l’actualisation.

Après avoir créé le `ListItem` s et ajoutez le bouton d’actualisation, la syntaxe déclarative s DropDownList et bouton doit ressembler à ce qui suit :


[!code-aspx[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample3.aspx)]

Avec tri DropDownList terminée, nous devons ensuite mettre à jour le s ObjectDataSource `Selecting` afin qu’elle utilise le Gestionnaire d’événements `SortBy``ListItem` s `Value` propriété par opposition à une expression de tri de codé en dur.


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample4.cs)]

À ce stade, lors de la première visite la page produits sont initialement triés par le `ProductName` champ de données, telle qu’elle s le `SortBy` `ListItem` sélectionné par défaut (voir Figure 6). Sélection d’une autre option comme catégorie de tri et en cliquant sur Actualiser pour provoquer une publication (postback) et nouveau tri des données par le nom de catégorie, comme le montre la Figure 7.


[![Les produits sont initialement triés par leur nom](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image15.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image14.png)

**Figure 6**: les produits sont initialement triés par leur nom ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image16.png))


[![Les produits sont maintenant triés par catégorie](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image18.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image17.png)

**Figure 7**: les produits sont maintenant triés par catégorie ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image19.png))


> [!NOTE]
> En cliquant sur le bouton d’actualisation entraîne les données automatiquement être retriée car l’état d’affichage du répéteur s a été désactivé, provoquant ainsi la répétition de lier de nouveau à sa source de données sur chaque publication (postback). Si vous avez déjà quitté l’état d’affichage du répéteur s activée, la modification de l’ordre de tri déroulante liste a gagné t a aucun effet sur l’ordre de tri. Pour résoudre ce problème, créez un gestionnaire d’événements pour le bouton Actualiser s `Click` reliaison répéteur à sa source de données et événements (en appelant le répéteur s `DataBind()` méthode).


## <a name="remembering-the-sort-expression-and-direction"></a>Mémorisation de l’Expression de tri et la Direction

Lors de la création d’un contrôle DataList ou un répéteur pouvant être trié sur une page où non-sort associées publications (postback) peut se produire, il impératif s que l’expression de tri et la direction être mémorisées entre publications (postback). Par exemple, imaginons que nous répéteur mis à jour de ce didacticiel pour inclure un bouton Supprimer avec chaque produit. Lorsque l’utilisateur clique sur le bouton Supprimer nous d exécuter du code pour supprimer le produit sélectionné, puis à relier les données dans le répéteur. Si les détails de tri ne sont pas conservées sur la publication (postback), les données affichées sur l’écran reviendra à l’ordre de tri d’origine.

Pour ce didacticiel, DropDownList implicitement enregistre le tri expression et le sens dans son état d’affichage pour nous. Si nous étions utilise une interface de tri différents un avec, par exemple, LinkButton qui a fourni les différentes options de tri d nous devons prendre soin de mémoriser l’ordre de tri entre les publications (postback). Cela peut être accompli en stockant les paramètres du tri dans l’état d’affichage de page s, en incluant le paramètre de tri dans la chaîne de requête, ou par une autre technique de persistance d’état.

Exemples futures de ce didacticiel Découvrez comment rendre persistantes les informations de tri dans l’état d’affichage de page s.

## <a name="step-5-adding-sorting-support-to-a-datalist-that-uses-default-paging"></a>Étape 5 : Ajout du tri de prise en charge pour un contrôle DataList qui utilise la pagination par défaut

Dans le [didacticiel précédent](paging-report-data-in-a-datalist-or-repeater-control-cs.md) nous avons examiné comment implémenter la pagination par défaut avec un contrôle DataList. Permettent d’étendre cet exemple précédent pour inclure la possibilité de trier les données paginées s. Commencez par ouvrir le `SortingWithDefaultPaging.aspx` et `Paging.aspx` des pages dans le `PagingSortingDataListRepeater` dossier. À partir de la `Paging.aspx` page, cliquez sur le bouton de la Source pour afficher le balisage déclaratif s de page. Copier le texte sélectionné (voir la Figure 8) et le coller dans le balisage déclaratif de `SortingWithDefaultPaging.aspx` entre les `<asp:Content>` balises.


[![Répliquer le balisage déclaratif dans le &lt;asp : Content&gt; balises de Paging.aspx à SortingWithDefaultPaging.aspx](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image21.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image20.png)

**Figure 8**: répliquer le balisage déclaratif dans le `<asp:Content>` balises `Paging.aspx` à `SortingWithDefaultPaging.aspx` ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image22.png))


Après avoir copié le balisage déclaratif, copiez les méthodes et propriétés dans le `Paging.aspx` classe code-behind de s à la classe code-behind pour la page `SortingWithDefaultPaging.aspx`. Ensuite, prenez un moment pour afficher la `SortingWithDefaultPaging.aspx` page dans un navigateur. Il doit présenter les mêmes fonctionnalités et la même apparence que `Paging.aspx`.

## <a name="enhancing-productsbll-to-include-a-default-paging-and-sorting-method"></a>Amélioration de ProductsBLL à inclure une valeur par défaut, la pagination et la méthode de tri

Dans le didacticiel précédent, nous avons créé un `GetProductsAsPagedDataSource(pageIndex, pageSize)` méthode dans le `ProductsBLL` classe qui a retourné un `PagedDataSource` objet. Cela `PagedDataSource` objet a été rempli avec *tous les* des produits (via la couche BLL s `GetProducts()` méthode), mais lorsqu’elle est liée au contrôle DataList uniquement les enregistrements correspondant à l’élément spécifié *pageIndex* et *pageSize* paramètres d’entrée ont été affichés.

Plus haut dans ce didacticiel, nous avons ajouté une prise en charge tri en spécifiant l’expression de tri à partir de la s ObjectDataSource `Selecting` Gestionnaire d’événements. Cela fonctionne bien quand ObjectDataSource est retourné un objet pouvant être triées, comme le `ProductsDataTable` retournée par le `GetProducts()` (méthode). Toutefois, le `PagedDataSource` objet retourné par la `GetProductsAsPagedDataSource` méthode ne prend pas en charge le tri de sa source de données interne. Au lieu de cela, nous avons besoin de trier les résultats retournés à partir de la `GetProducts()` méthode *avant* nous placer dans le `PagedDataSource`.

Pour ce faire, créez une méthode dans le `ProductsBLL` (classe), `GetProductsSortedAsPagedDataSource(sortExpression, pageIndex, pageSize)`. Pour trier les `ProductsDataTable` retourné par le `GetProducts()` (méthode), spécifiez la `Sort` propriété de sa valeur par défaut `DataTableView`:


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample5.cs)]

Le `GetProductsSortedAsPagedDataSource` méthode diffère légèrement de la `GetProductsAsPagedDataSource` méthode créé dans le didacticiel précédent. En particulier, `GetProductsSortedAsPagedDataSource` accepte un paramètre d’entrée supplémentaire `sortExpression` et affecte cette valeur à la `Sort` propriété de la `ProductDataTable` s `DefaultView`. Quelques lignes de code d’une version ultérieure, le `PagedDataSource` objet s source de données est assigné le `ProductDataTable` s `DefaultView`.

## <a name="calling-the-getproductssortedaspageddatasource-method-and-specifying-the-value-for-the-sortexpression-input-parameter"></a>Appel de la méthode GetProductsSortedAsPagedDataSource et en spécifiant la valeur pour le paramètre d’entrée de SortExpression

Avec la `GetProductsSortedAsPagedDataSource` terminée, l’étape suivante consiste à fournir la valeur de ce paramètre. Dans ObjectDataSource `SortingWithDefaultPaging.aspx` est actuellement configuré pour appeler le `GetProductsAsPagedDataSource` méthode et passe dans les deux paramètres d’entrée via ses deux `QueryStringParameters`, qui sont spécifié dans le `SelectParameters` collection. Ces deux `QueryStringParameters` indiquer que la source pour le `GetProductsAsPagedDataSource` méthode s *pageIndex* et *pageSize* paramètres proviennent les champs de chaîne de requête `pageIndex` et `pageSize`.

Mettre à jour le s ObjectDataSource `SelectMethod` propriété afin qu’elle appelle la nouvelle `GetProductsSortedAsPagedDataSource` (méthode). Ensuite, ajoutez un nouvel `QueryStringParameter` afin que le *sortExpression* paramètre d’entrée est accessible à partir du champ querystring `sortExpression`. Définir le `QueryStringParameter` s `DefaultValue` ProductName.

Après ces modifications, le balisage déclaratif de s ObjectDataSource doit ressembler à :


[!code-aspx[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample6.aspx)]

À ce stade, le `SortingWithDefaultPaging.aspx` page trie ses résultats par ordre alphabétique par le nom du produit (voir la Figure 9). C’est pourquoi, par défaut, une valeur de ProductName est passée en tant que le `GetProductsSortedAsPagedDataSource` méthode s *sortExpression* paramètre.


[![Par défaut, les résultats sont triés par nom de produit](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image24.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image23.png)

**Figure 9**: par défaut, les résultats sont triés par `ProductName` ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image25.png))


Si vous ajoutez manuellement un `sortExpression` champ querystring comme `SortingWithDefaultPaging.aspx?sortExpression=CategoryName` les résultats sont triés par le `sortExpression`. Toutefois, cela `sortExpression` paramètre n’est pas inclus dans la chaîne de requête lors du déplacement vers une autre page de données. En fait, en cliquant sur la page suivante ou dernière des boutons nous revenez dans `Paging.aspx`! En outre, s’il n’y a actuellement aucun tri de l’interface. Un utilisateur peut modifier l’ordre de tri des données paginées impérativement en manipulant directement de la chaîne de requête.

## <a name="creating-the-sorting-interface"></a>Création de l’Interface de tri

Nous devons tout d’abord mettre à jour le `RedirectUser` méthode pour envoyer à l’utilisateur `SortingWithDefaultPaging.aspx` (au lieu de `Paging.aspx`) et pour inclure la `sortExpression` valeur dans la chaîne de requête. Nous devons également ajouter un en lecture seule, de niveau page nommé `SortExpression` propriété. Cette propriété, similaire à la `PageIndex` et `PageSize` propriétés créées dans le didacticiel précédent, retourne la valeur de la `sortExpression` champ querystring si elle existe et la valeur par défaut de valeur (ProductName) dans le cas contraire.

Actuellement le `RedirectUser` méthode accepte uniquement un paramètre d’entrée unique l’index de la page à afficher. Toutefois, il peut arriver que vous souhaitiez rediriger l’utilisateur vers une page de données à l’aide d’une expression de tri que Nouveautés spécifiée dans la chaîne de requête spécifique. Dans un instant, nous allons créer l’interface de tri de cette page, ce qui inclut une série de contrôles de bouton Web pour trier les données d’une colonne spécifiée. Lorsqu’un de ces boutons est activé, vous souhaitez rediriger l’utilisateur, en passant la valeur de l’expression de tri approprié. Pour fournir cette fonctionnalité, créez deux versions de la `RedirectUser` (méthode). Premier doit accepter uniquement l’index de page à afficher, tandis que l’autre accepte l’expression de tri et les index de page.


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample7.cs)]

Dans le premier exemple dans ce didacticiel, nous avons créé une interface de tri à l’aide d’une liste déroulante. Pour cet exemple, s permettent d’utiliser trois contrôles de bouton Web au-dessus du contrôle DataList une pour le tri par `ProductName`, un pour `CategoryName`et l’autre pour `SupplierName`. Ajoutez les trois contrôles bouton Web, en définissant leurs `ID` et `Text` propriétés correctement :


[!code-aspx[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample8.aspx)]

Ensuite, créez un `Click` pour chaque gestionnaire d’événements. Les gestionnaires d’événements doivent appeler le `RedirectUser` méthode, en retournant l’utilisateur à la première page à l’aide de l’expression de tri approprié.


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample9.cs)]

Lors de la première visite de la page, les données sont triées par ordre alphabétique par nom de produit (voir la Figure 9). Cliquez sur le bouton suivant pour passer à la deuxième page de données, puis sur le tri par le bouton de la catégorie. Cela nous ramène à la première page de données, triées par nom de catégorie (voir la Figure 10). De même, en cliquant sur le tri par bouton de fournisseur trie les données par le fournisseur à partir de la première page de données. Le choix de tri est mémorisé comme les données sont paginées via. Figure 11 illustre la page après le tri par catégorie et puis menant à la page treizième de données.


[![Les produits sont triés par catégorie](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image27.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image26.png)

**La figure 10**: les produits sont triées par catégorie ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image28.png))


[![L’Expression de tri est mémorisée lors de la pagination via les données](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image30.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image29.png)

**Figure 11**: l’Expression de tri est mémorisée lors de la pagination via les données ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image31.png))


## <a name="step-6-custom-paging-through-records-in-a-repeater"></a>Étape 6 : La pagination personnalisée dans les enregistrements dans un répéteur.

L’exemple de DataList examinées à l’étape 5 pages via ses données à l’aide de la technique de la pagination par défaut inefficace. Lors de la pagination via suffisamment grandes quantités de données, il est impératif que la pagination personnalisée est utilisée. Dans le [efficacement la pagination via grandes quantités de données](../paging-and-sorting/efficiently-paging-through-large-amounts-of-data-cs.md) et [tri des données de la réserve paginée personnalisé](../paging-and-sorting/sorting-custom-paged-data-cs.md) didacticiels, nous avons examiné les différences entre la pagination personnalisée et méthodes créés par défaut et dans la couche BLL pour utilisation de la pagination et le tri des données paginées personnalisées personnalisé. En particulier, dans ces deux didacticiels précédents, nous avons ajouté les trois méthodes suivantes pour la `ProductsBLL` classe :

- `GetProductsPaged(startRowIndex, maximumRows)`Retourne un sous-ensemble particulier d’enregistrements en commençant à *startRowIndex* et ne dépassant ne pas *maximumRows*.
- `GetProductsPagedAndSorted(sortExpression, startRowIndex, maximumRows)`Retourne un sous-ensemble particulier d’enregistrements triés par le *sortExpression* paramètre d’entrée.
- `TotalNumberOfProducts()`fournit le nombre total d’enregistrements dans la `Products` table de base de données.

Ces méthodes peuvent être utilisées à la page et de tri des données à l’aide d’un contrôle DataList ou répéteur efficacement. Pour illustrer cela, permettent de s commencez par créer un contrôle de répéteur avec prise en charge la pagination personnalisée ; Nous allons ensuite ajouter des fonctionnalités de tri.

Ouvrir le `SortingWithCustomPaging.aspx` page dans le `PagingSortingDataListRepeater` dossier et ajoutez un répéteur à la page, son `ID` propriété `Products`. À partir de la balise active de répéteur s, créez un nouveau ObjectDataSource nommé `ProductsDataSource`. Configurer pour sélectionnez ses données à partir de la `ProductsBLL` classe s `GetProductsPaged` (méthode).


[![Configurer pour utiliser la méthode GetProductsPaged de la classe ProductsBLL s ObjectDataSource](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image33.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image32.png)

**Figure 12**: configurer l’ObjectDataSource à utiliser le `ProductsBLL` classe s `GetProductsPaged` (méthode) ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image34.png))


Définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None) et puis cliquez sur le bouton suivant. L’Assistant Configurer la Source de données invite désormais pour les sources de la `GetProductsPaged` méthode s *startRowIndex* et *maximumRows* paramètres d’entrée. En réalité, ces paramètres d’entrée sont ignorées. Au lieu de cela, le *startRowIndex* et *maximumRows* valeurs sont transmises dans les `Arguments` propriété dans le s ObjectDataSource `Selecting` Gestionnaire d’événements, tout comme la façon dont nous avons spécifié le *sortExpression* dans cette démonstration premier didacticiel s. Par conséquent, laissez le paramètre source listes déroulantes dans l’Assistant défini sur aucun.


[![Conservez le paramètre Sources None](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image36.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image35.png)

**Figure 13**: laisser l’ensemble de Sources de paramètre None ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image37.png))


> [!NOTE]
> Faire *pas* définir le s ObjectDataSource `EnablePaging` propriété `true`. Cela entraîne l’ObjectDataSource inclure automatiquement son propre *startRowIndex* et *maximumRows* paramètres pour le `SelectMethod` liste paramètre existant. Le `EnablePaging` propriété est utile lors de la liaison personnalisée, car ces contrôles attendent un comportement particulier de ObjectDataSource s de données à un contrôle GridView, DetailsView ou FormView paginées uniquement disponible quand `EnablePaging` propriété est `true`. Étant donné que nous devons manuellement ajouter la prise en charge de la pagination pour le contrôle DataList et répéteur, laissez cette propriété `false` (la valeur par défaut), comme nous allons gâteaux dans les fonctionnalités nécessaires directement au sein de notre page relative à ASP.NET.


Pour finir, définissez le répéteur s `ItemTemplate` afin que le nom de produit s, la catégorie et le fournisseur sont affichés. Après ces modifications, la syntaxe déclarative s répéteur et ObjectDataSource doit ressembler à ce qui suit :


[!code-aspx[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample10.aspx)]

Prenez un moment pour visiter la page via un navigateur et notez qu’aucun enregistrement n’est retournés. C’est parce que nous ve encore pour spécifier le *startRowIndex* et *maximumRows* des valeurs de paramètres ; par conséquent, les valeurs de 0 sont passées dans les deux. Pour spécifier ces valeurs, créez un gestionnaire d’événements pour ObjectDataSource s `Selecting` événement et définissez ces paramètres de valeurs par programmation à des valeurs codées en dur de 0 à 5, respectivement :


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample11.cs)]

Avec cette modification, la page, via un navigateur, affiche les cinq premiers produits.


[![La première des cinq enregistrements sont affichés.](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image39.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image38.png)

**La figure 14**: le premier cinq enregistrements sont affichés ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image40.png))


> [!NOTE]
> Les produits répertoriés dans la Figure 14 se produisent à trier par nom de produit, car le `GetProductsPaged` procédure stockée qui effectue la requête de la pagination personnalisée efficace trie les résultats par `ProductName`.


Pour autoriser l’utilisateur à parcourir les pages, nous devons effectuer le suivi de l’index de ligne de début et le nombre maximal de lignes et n’oubliez pas ces valeurs sur des publications. Dans l’exemple de la pagination par défaut, nous avons utilisé les champs de chaîne de requête pour conserver ces valeurs ; pour cette démonstration, permettent de conserver ces informations dans l’état d’affichage de page s s. Créez les deux propriétés suivantes :


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample12.cs)]

Ensuite, mettez à jour le code dans le Gestionnaire d’événements si vous sélectionnez afin qu’il utilise le `StartRowIndex` et `MaximumRows` propriétés au lieu des valeurs codées en dur de 0 à 5 :


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample13.cs)]

À ce stade notre page affiche toujours uniquement les cinq premiers enregistrements. Cependant, si ces propriétés en place, nous re prêt à créer notre interface de pagination.

## <a name="adding-the-paging-interface"></a>Ajout de l’Interface de pagination

Let utilise le même premier, précédent, en regard, dernier la pagination de l’interface utilisée dans l’exemple de la pagination par défaut, y compris le Web de l’étiquette de contrôle qui affiche la page de données est affiché et le nombre total de pages existent. Ajoutez les quatre contrôles bouton Web et étiquette en dessous de répéteur.


[!code-aspx[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample14.aspx)]

Ensuite, créez `Click` gestionnaires d’événements pour les quatre boutons. Lorsqu’un de ces boutons est activé, nous devons mettre à jour le `StartRowIndex` et lier de nouveau les données dans le répéteur. Le code des première, boutons Précédent et suivant est assez simple, mais pour le dernier bouton comment nous déterminer l’index de ligne de début de la dernière page de données ? Pour calculer cet index, ainsi que la possibilité de déterminer si les boutons suivant et dernier doivent être activés que nous devons connaître le nombre d’enregistrements au total est en cours de pagination via. Nous pouvons le déterminer en appelant le `ProductsBLL` classe s `TotalNumberOfProducts()` (méthode). S permettent de créer une propriété en lecture seule, de niveau page nommée `TotalRowCount` qui retourne les résultats de la `TotalNumberOfProducts()` méthode :


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample15.cs)]

Avec cette propriété, nous pouvons maintenant déterminer le dernier index de ligne de début s de page. Plus précisément, elle s le résultat entier de la `TotalRowCount` moins 1 divisée par `MaximumRows`, multipliée par `MaximumRows`. Nous pouvons maintenant écrire le `Click` gestionnaires d’événements pour les quatre boutons d’interface de pagination :


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample16.cs)]

Enfin, nous devons désactiver les boutons de premier et précédent dans l’interface de pagination lors de l’affichage de la première page de données et les boutons suivant et dernier lors de l’affichage de la dernière page. Pour ce faire, ajoutez le code suivant au s ObjectDataSource `Selecting` Gestionnaire d’événements :


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample17.cs)]

Après avoir ajouté ces `Click` gestionnaires d’événements et le code pour activer ou désactiver les éléments d’interface de pagination en fonction de l’index de ligne de début actuelle, la page de test dans un navigateur. Lorsque la Figure 15 illustre, lors de la première visite la page de la première et les boutons précédent sont désactivées. En cliquant sur suivant montre la deuxième page de données, tandis qu’en cliquant sur le dernier affiche la dernière page (voir Figures 16 et 17). Lors de l’affichage de la dernière page de données, le suivant et le dernier boutons sont désactivés.


[![Les boutons Précédent et dernière sont désactivées lors de l’affichage de la première Page de produits](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image42.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image41.png)

**Figure 15**: la précédente et dernier boutons sont désactivés lors de l’affichage de la première Page de produits ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image43.png))


[![La deuxième Page de produits sont Dispalyed](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image45.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image44.png)

**Figure 16**: la deuxième Page de produits sont Dispalyed ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image46.png))


[![En cliquant sur la dernière affiche la dernière Page de données](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image48.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image47.png)

**Figure 17**: en cliquant sur le dernier affiche la dernière Page de données ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image49.png))


## <a name="step-7-including-sorting-support-with-the-custom-paged-repeater"></a>Étape 7 : Y compris le tri de la prise en charge avec personnalisé paginée répéteur

Maintenant que la pagination personnalisée a été implémentée, nous re prêt à inclure le tri en charge. Le `ProductsBLL` classe s `GetProductsPagedAndSorted` méthode a le même *startRowIndex* et *maximumRows* d’entrée de paramètres en tant que `GetProductsPaged`, mais permet à un autre  *sortExpression* paramètre d’entrée. Pour utiliser le `GetProductsPagedAndSorted` méthode à partir de `SortingWithCustomPaging.aspx`, nous avons besoin effectuer les étapes suivantes :

1. Modifier le s ObjectDataSource `SelectMethod` propriété à partir de `GetProductsPaged` à `GetProductsPagedAndSorted`.
2. Ajouter un *sortExpression* `Parameter` objet au s ObjectDataSource `SelectParameters` collection.
3. Créer une privée, au niveau des pages `SortExpression` propriété qui conserve sa valeur entre publications (postback) à l’état d’affichage de page s.
4. Mettre à jour le s ObjectDataSource `Selecting` Gestionnaire d’événements pour affecter le s ObjectDataSource *sortExpression* paramètre la valeur de niveau de la page `SortExpression` propriété.
5. Créez l’interface de tri.

Commencez par mettre à jour le s ObjectDataSource `SelectMethod` propriété et en ajoutant un *sortExpression* `Parameter`. Assurez-vous que le *sortExpression* `Parameter` s `Type` est définie sur `String`. Après avoir effectué ces deux premières tâches, le balisage déclaratif de s ObjectDataSource doit se présenter comme suit :


[!code-aspx[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample18.aspx)]

Ensuite, nous avons besoin de niveau page `SortExpression` propriété dont la valeur est sérialisée à l’état d’affichage. Si aucune valeur d’expression de tri n’a été défini, utilisez ProductName en tant que la valeur par défaut :


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample19.cs)]

Avant que l’ObjectDataSource appelle la `GetProductsPagedAndSorted` méthode que nous devons définir le *sortExpression* `Parameter` à la valeur de la `SortExpression` propriété. Dans la `Selecting` Gestionnaire d’événements, ajoutez la ligne de code suivante :


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample20.cs)]

Ne reste qu’à implémenter l’interface de tri. Comme nous l’avons fait dans le dernier exemple, permettent de s que l’interface de tri implémenté à l’aide de trois contrôles de bouton Web permettant aux utilisateurs de trier les résultats par nom de produit, catégorie ou le fournisseur.


[!code-aspx[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample21.aspx)]

Créer `Click` gestionnaires d’événements pour ces trois contrôles de bouton. Dans l’événement gestionnaire, réinitialiser le `StartRowIndex` à 0, définissez la `SortExpression` à la valeur appropriée et les données à la répétition de la reliaison :


[!code-csharp[Main](sorting-data-in-a-datalist-or-repeater-control-cs/samples/sample22.cs)]

Il est tout s ! Alors qu’il y a un nombre d’étapes de la pagination personnalisée et tri implémenté, les étapes ont été très similaires à celles nécessaires pour la pagination par défaut. La figure 18 montre les produits lors de l’affichage de la dernière page de données triés par catégorie.


[![La dernière Page de données, trié par catégorie, s’affiche.](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image51.png)](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image50.png)

**La figure 18**: la dernière Page de données, trié par catégorie, s’affiche ([cliquez pour afficher l’image en taille réelle](sorting-data-in-a-datalist-or-repeater-control-cs/_static/image52.png))


> [!NOTE]
> Dans les exemples précédents, lors du tri par le fournisseur de que nom a été utilisé en tant que l’expression de tri. Toutefois, pour l’implémentation de la pagination personnalisée, nous devons utiliser CompanyName. C’est parce que la procédure stockée chargée d’implémenter la pagination personnalisée `GetProductsPagedAndSorted` transmet l’expression de tri dans le `ROW_NUMBER()` (mot clé), le `ROW_NUMBER()` (mot clé) nécessite le nom de colonne réel plutôt qu’un alias. Par conséquent, nous devons utiliser `CompanyName` (le nom de la colonne dans la `Suppliers` table) au lieu de l’alias utilisé dans le `SELECT` requête (`SupplierName`) pour l’expression de tri.


## <a name="summary"></a>Résumé

Ni la DataList ni répéteur offrent la prise en charge du tri intégrée, mais avec un peu de code et une interface de tri personnalisée, ces fonctionnalités peuvent être ajoutées. Lorsque vous implémentez le tri, mais ne pas la pagination, l’expression de tri peut être spécifiée via la `DataSourceSelectArguments` objet passé dans le s ObjectDataSource `Select` (méthode). Cela `DataSourceSelectArguments` objet s `SortExpression` propriété peut être attribuée dans le s ObjectDataSource `Selecting` Gestionnaire d’événements.

Pour ajouter des fonctionnalités de tri à un contrôle DataList ou un répéteur qui fournit déjà la prise en charge la pagination, la plus simple consiste à personnaliser la couche de logique métier pour inclure une méthode qui accepte une expression de tri. Ces informations peuvent ensuite être transmises dans un paramètre dans le s ObjectDataSource `SelectParameters`.

Ce didacticiel termine notre examen de la pagination et le tri avec les contrôles DataList et répéteur. Notre didacticiel suivant et dernière examine comment ajouter des contrôles de bouton Web aux modèles s DataList et répéteur afin de fournir des fonctionnalités personnalisées, initiée par l’utilisateur sur une base par élément.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été David Suru. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](paging-report-data-in-a-datalist-or-repeater-control-cs.md)
[Suivant](paging-report-data-in-a-datalist-or-repeater-control-vb.md)
