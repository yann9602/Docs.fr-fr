---
uid: web-forms/overview/data-access/paging-and-sorting/creating-a-customized-sorting-user-interface-cs
title: "Création d’une Interface utilisateur de tri personnalisées (c#) | Documents Microsoft"
author: rick-anderson
description: "Lors de l’affichage d’une longue liste de triée des données, il peut être très utile de regrouper les données associées en introduisant des lignes de séparation. Dans ce didacticiel, nous verrons comment cre..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/15/2006
ms.topic: article
ms.assetid: 6f81b633-9d01-4e52-ae4a-2ea6bc109475
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/paging-and-sorting/creating-a-customized-sorting-user-interface-cs
msc.type: authoredcontent
ms.openlocfilehash: bc009272df3626402ee4c52578f9b364f70a4e78
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-customized-sorting-user-interface-c"></a>Création d’une Interface utilisateur de tri personnalisées (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_27_CS.exe) ou [télécharger le PDF](creating-a-customized-sorting-user-interface-cs/_static/datatutorial27cs1.pdf)

> Lors de l’affichage d’une longue liste de triée des données, il peut être très utile de regrouper les données associées en introduisant des lignes de séparation. Dans ce didacticiel, nous verrons comment créer une interface utilisateur tri.


## <a name="introduction"></a>Introduction

Affichage d’une longue liste de lorsque les données triées où il existe seulement quelques valeurs différentes dans la colonne triée, un utilisateur final peut s’avérer difficile d’identifier où, exactement, les limites de différence se produisent. Par exemple, il existe des 81 produits dans la base de données, mais les choix de catégories différentes neuf (huit catégories uniques ainsi que les `NULL` option). Prenons le cas d’un utilisateur souhaitant en examinant les produits qui se trouvent sous la catégorie Seafood. À partir d’une page qui répertorie *tous les* des produits dans un GridView unique, l’utilisateur peut décider de son meilleure solution consiste à trier les résultats par catégorie, qui regroupe tous les produits Seafood ensemble. Après le tri par catégorie, les utilisateurs doivent à la recherche dans la liste, recherchez où les produits regroupés en Seafood commencer et se terminer. Étant donné que les résultats sont triés par ordre alphabétique par nom de la catégorie recherche les produits Seafood n’est pas difficile, mais elle nécessite toujours étroitement l’analyse de la liste des éléments dans la grille.

Pour vous aider à mettre en surbrillance les limites entre groupes triés, de nombreux sites Web utilisent une interface utilisateur qui ajoute un séparateur entre ces groupes. Séparateurs telles que celles affichées dans la Figure 1 permet à un utilisateur plus rapidement trouver un groupe particulier et identifier ses limites, mais aussi déterminer à quels groupes distincts existent dans les données.


[![Chaque groupe de catégories est clairement identifiés](creating-a-customized-sorting-user-interface-cs/_static/image2.png)](creating-a-customized-sorting-user-interface-cs/_static/image1.png)

**Figure 1**: chaque groupe de catégories est clairement ([cliquez pour afficher l’image en taille réelle](creating-a-customized-sorting-user-interface-cs/_static/image3.png))


Dans ce didacticiel, nous verrons comment créer une interface utilisateur tri.

## <a name="step-1-creating-a-standard-sortable-gridview"></a>Étape 1 : Création d’un GridView Standard et pouvant être trié

Avant d’Explorer comment augmenter le GridView pour fournir l’interface de tri améliorée, permettent de s tout d’abord créer un GridView standard et pouvant être trié qui répertorie les produits. Commencez par ouvrir le `CustomSortingUI.aspx` page dans le `PagingAndSorting` dossier. Ajouter un contrôle GridView à la page, définissez son `ID` propriété `ProductList`et le lier à un nouveau ObjectDataSource. Configurer l’ObjectDataSource à utiliser le `ProductsBLL` classe s `GetProducts()` méthode de sélection des enregistrements.

Ensuite, configurez le contrôle GridView tel qu’il ne contienne la `ProductName`, `CategoryName`, `SupplierName`, et `UnitPrice` BoundFields et le CheckBoxField abandonné. Enfin, configurez le contrôle GridView pour prendre en charge le tri en activant la case à cocher Activer le tri dans la balise active de s GridView (ou en définissant sa `AllowSorting` propriété `true`). Après avoir apporté ces ajouts à la `CustomSortingUI.aspx` page, le balisage déclaratif doit ressembler à ce qui suit :


[!code-aspx[Main](creating-a-customized-sorting-user-interface-cs/samples/sample1.aspx)]

Prenez un moment pour consulter notre progression jusqu'à présent dans un navigateur. La figure 2 illustre le GridView sortable lorsque ses données sont triées par catégorie dans l’ordre alphabétique.


[![Les s GridView pouvant être trié les données sont triées par catégorie](creating-a-customized-sorting-user-interface-cs/_static/image5.png)](creating-a-customized-sorting-user-interface-cs/_static/image4.png)

**Figure 2**: s GridView Sortable données sont triées par catégorie ([cliquez pour afficher l’image en taille réelle](creating-a-customized-sorting-user-interface-cs/_static/image6.png))


## <a name="step-2-exploring-techniques-for-adding-the-separator-rows"></a>Étape 2 : Exploration des Techniques permettant d’ajouter les lignes de séparation

Avec GridView générique, pouvant être trié terminé, ne reste qu’à être en mesure d’ajouter les lignes de séparation dans le GridView, avant de chaque groupe trié. Mais comment ces lignes possible d’injecter dans le GridView ? Essentiellement, nous devons parcourir les lignes s GridView, déterminer où les différences entre les valeurs dans la colonne triée, puis ajoutez la ligne de séparation appropriés. Quand vous réfléchissez à ce problème, cela semble naturel que la solution se trouve quelque part dans le GridView s `RowDataBound` Gestionnaire d’événements. Comme expliqué dans la [personnalisé de mise en forme à données](../custom-formatting/custom-formatting-based-upon-data-cs.md) didacticiel, ce gestionnaire d’événements est utilisé couramment lors de la mise en forme au niveau des lignes basé sur les données de ligne s. Toutefois, le `RowDataBound` Gestionnaire d’événements n’est pas la solution, lorsque des lignes ne peuvent pas être ajoutées au GridView par programme à partir de ce gestionnaire d’événements. Les s GridView `Rows` collection est en fait, en lecture seule.

Pour ajouter des lignes supplémentaires au GridView nous avez trois possibilités :

- Ajoutez ces lignes de séparation de métadonnées pour les données réelles qui sont liées au contrôle GridView
- Une fois que le contrôle GridView a été lié aux données, ajouter d’autres `TableRow` instances au GridView s contrôlent la collecte
- Créer un contrôle serveur personnalisé qui étend le contrôle GridView et substitue les méthodes responsables de la construction de la structure de s GridView

Création d’un contrôle serveur personnalisé est la meilleure approche si cette fonctionnalité était nécessaire sur de nombreuses pages web ou sur plusieurs sites Web. Toutefois, il faudrait un peu de code et une exploration approfondie dans les profondeurs des mécanismes internes s GridView. Par conséquent, nous étudierons pas cette option pour ce didacticiel.

Les deux autres options séparateur Ajout d’une ligne pour les données réelles qui est lié au GridView et manipuler la collection de contrôle GridView s après son été lié - attaquer le problème différemment et méritent une explication.

## <a name="adding-rows-to-the-data-bound-to-the-gridview"></a>Ajout de lignes aux données liées au GridView

Lorsque le contrôle GridView est lié à une source de données, il crée un `GridViewRow` pour chaque enregistrement retourné par la source de données. Par conséquent, nous pouvons injecter les lignes de séparation nécessaires en ajoutant des enregistrements de séparateur à la source de données avant de le lier au contrôle GridView. La figure 3 illustre ce concept.


![L’une des techniques consiste à ajouter des lignes de séparateur à la Source de données](creating-a-customized-sorting-user-interface-cs/_static/image7.png)

**Figure 3**: l’une des techniques consiste à ajouter des lignes de séparateur à la Source de données


Utiliser les enregistrements de séparateur à terme entre guillemets, car il n’existe aucun enregistrement de séparation particulière ; au lieu de cela, nous devons une certaine manière indicateur servant à un enregistrement particulier dans la source de données comme un séparateur, plutôt qu’une ligne de données normale. Pour nos exemples, nous re liaison un `ProductsDataTable` instance pour le GridView, qui se compose de `ProductRows`. Nous pouvons marquer un enregistrement en tant que séparateur ligne en définissant son `CategoryID` propriété `-1` (étant donné que ces une valeur n’est pas parvenu existe normalement).

Pour utiliser cette technique d nous devons effectuer les étapes suivantes :

1. Récupérer par programme les données à lier au contrôle GridView (un `ProductsDataTable` instance)
2. Trier les données selon le GridView s `SortExpression` et `SortDirection` propriétés
3. Itérer au sein du `ProductsRows` dans les `ProductsDataTable`, où se situent les différences dans la colonne triée à la recherche
4. À la limite de chaque groupe, injecter un enregistrement de séparateur `ProductsRow` instance dans le DataTable, elle possède s `CategoryID` la valeur `-1` (ou toute désignation a été décidée pour marquer un enregistrement comme un enregistrement de séparateur)
5. Après avoir injecte les lignes de séparation, lier par programmation les données au contrôle GridView

En plus de ces cinq étapes, nous d devez également fournir un gestionnaire d’événements pour s GridView `RowDataBound` événement. Ici, nous d vérifier chaque `DataRow` et déterminer si elle a un séparateur de ligne, une dont `CategoryID` paramètre était `-1`. Dans ce cas, nous d souhaiterez probablement ajuster sa mise en forme ou le texte affiché dans l’ou les cellules.

À l’aide de cette technique pour injecter les limites du groupe tri requiert un peu plus de travail que décrites ci-dessus, que vous devez également fournir un gestionnaire d’événements pour s GridView `Sorting` effectuer le suivi de l’événement et conserver de le `SortExpression` et `SortDirection` valeurs.

## <a name="manipulating-the-gridview-s-control-collection-after-it-s-been-databound"></a>Manipuler le s GridView contrôler la collecte après lui s été lié aux données

Plutôt que les données de messagerie avant de le lier au contrôle GridView, nous pouvons ajouter les lignes de séparation *après* l’a été lié aux données au GridView. Le processus de liaison de données génère la hiérarchie de contrôle GridView s, qui est en réalité simplement `Table` instance composées d’une collection de lignes, chacune d’elles est composé d’une collection de cellules. Plus précisément, la collection de contrôles GridView s contient un `Table` objet racine, un `GridViewRow` (qui est dérivée de la `TableRow` classe) pour chaque enregistrement dans le `DataSource` lié au GridView et un `TableCell` objet dans chaque `GridViewRow` instance pour chaque champ de données dans le `DataSource`.

Pour ajouter des lignes de séparation entre chaque groupe de tri, nous pouvons manipuler directement cette hiérarchie de contrôle une fois qu’elle a été créée. Il se peut que nous pouvons être certain que la hiérarchie de contrôle GridView s a été créée pour la dernière fois au moment que du rendu de la page. Par conséquent, cette méthode remplace la `Page` classe s `Render` méthode, alors la hiérarchie de contrôle final s GridView est mis à jour pour inclure les lignes de séparation nécessaires. La figure 4 illustre ce processus.


[![Une autre Technique manipule la hiérarchie de contrôle GridView s](creating-a-customized-sorting-user-interface-cs/_static/image9.png)](creating-a-customized-sorting-user-interface-cs/_static/image8.png)

**Figure 4**: une autre Technique manipule la hiérarchie des contrôles de s GridView ([cliquez pour afficher l’image en taille réelle](creating-a-customized-sorting-user-interface-cs/_static/image10.png))


Pour ce didacticiel, nous utiliserons cette dernière approche pour personnaliser l’expérience utilisateur de tri.

> [!NOTE]
> Le code je m présenter dans ce didacticiel est basé sur l’exemple fourni dans [Teemu Keiski](http://aspadvice.com/blogs/joteke/default.aspx) entrée de blog s [lecture Bit avec GridView tri regroupement](http://aspadvice.com/blogs/joteke/archive/2006/02/11/15130.aspx).


## <a name="step-3-adding-the-separator-rows-to-the-gridview-s-control-hierarchy"></a>Étape 3 : Ajout du séparateur de lignes à la hiérarchie de contrôle GridView s

Étant donné que nous souhaitons uniquement ajouter les lignes de séparation dans la hiérarchie de contrôle GridView s après que sa hiérarchie de contrôle a été créé et créé pour la dernière fois sur cette visite de la page, nous souhaitons effectuer cet ajout à la fin du cycle de vie de page, mais avant le c GridView réel hiérarchie de contrôle a été rendu en HTML. Le dernier point possible à laquelle nous pouvons effectuer cette opération est la `Page` classe s `Render` événement, nous pouvons remplacer dans notre classe code-behind à l’aide de la signature de méthode suivante :


[!code-csharp[Main](creating-a-customized-sorting-user-interface-cs/samples/sample2.cs)]

Lorsque le `Page` classe d’origine `Render` méthode est appelée `base.Render(writer)` chacun des contrôles dans la page est restituée, générer le balisage en fonction de leur hiérarchie de contrôle. Par conséquent, il est impératif que nous les appelons `base.Render(writer)`, de sorte que la page est rendue, hiérarchie avant d’appeler des contrôles et de manipuler des s GridView `base.Render(writer)`, de sorte que les lignes de séparation ont été ajoutés à la hiérarchie de contrôle GridView s avant s été rendu.

Pour insérer les en-têtes de groupe de tri nous devons d’abord vous assurer que l’utilisateur a demandé que les données triées. Par défaut, le contenu de s GridView n’est pas trié, et par conséquent, nous n’avez pas besoin d’entrer n’importe quel groupe de tri des en-têtes.

> [!NOTE]
> Si vous souhaitez que le contrôle GridView à trier par une colonne particulière lors du premier chargement de la page, appelez le s GridView `Sort` méthode sur la première visite de page (mais pas sur les publications ultérieures). Pour ce faire, ajoutez cet appel dans le `Page_Load` Gestionnaire d’événements dans un `if (!Page.IsPostBack)` conditionnel. Faire référence à la [la pagination et le tri des données de rapport](paging-and-sorting-report-data-cs.md) informations didacticiels pour plus d’informations sur la `Sort` (méthode).


En supposant que les données ont été triées, la tâche suivante consiste à déterminer les colonnes, les données a été triées par et pour analyser les lignes de la recherche des différences dans la colonne s les valeurs. Le code suivant permet de s’assurer que les données ont été triées et recherche la colonne par laquelle les données ont été triées :


[!code-csharp[Main](creating-a-customized-sorting-user-interface-cs/samples/sample3.cs)]

Si le contrôle GridView présente encore être triées, le s GridView `SortExpression` propriété n’est pas été définie. Par conséquent, nous souhaitons uniquement ajouter les lignes de séparation si cette propriété a une valeur. Dans ce cas, nous devons ensuite déterminer l’index de la colonne selon laquelle les données a été triées. Pour ce faire, vous devez exécuter une boucle dans le GridView s `Columns` collection, la recherche de la colonne dont `SortExpression` propriété est égale à la s GridView `SortExpression` propriété. En plus de l’index de la colonne s, nous extrayons également le `HeaderText` propriété, qui est utilisé lors de l’affichage des lignes de séparation.

L’index de la colonne par laquelle les données sont triées, l’étape finale est pour énumérer les lignes du contrôle GridView. Pour chaque ligne, nous devons déterminer si la colonne triée s valeur diffère du précédent ligne s triées colonne s. Si nous devons donc d’injecter un nouveau `GridViewRow` instance dans la hiérarchie des contrôles. Cela est accompli par le code suivant :


[!code-csharp[Main](creating-a-customized-sorting-user-interface-cs/samples/sample4.cs)]

Ce code commence par référencer par programme le `Table` de l’objet trouvé à la racine de la hiérarchie de contrôle GridView s et la création d’une variable chaîne nommée `lastValue`. `lastValue`permet de comparer la valeur de colonne s triées de ligne actuelle avec la valeur précédente de la ligne s. Ensuite, le contrôle GridView s `Rows` collection est énumérée, et pour chaque ligne, la valeur de la colonne triée est stockée dans le `currentValue` variable.

> [!NOTE]
> Pour déterminer la valeur de la colonne triée ligne particulière utiliser la cellule s `Text` propriété. Cela fonctionne bien pour BoundFields, mais ne sera pas fonctionnent comme vous le souhaitez pour TemplateField, CheckBoxFields et ainsi de suite. Nous allons examiner comment faire pour prendre en compte pour les autres champs de GridView peu de temps.


Le `currentValue` et `lastValue` variables sont ensuite comparés. S’ils sont différents, nous devons ajouter une nouvelle ligne de séparation dans la hiérarchie de contrôle. Pour ce faire, vous devez déterminer l’index de la `GridViewRow` dans les `Table` objet s `Rows` collection, la création de nouveaux `GridViewRow` et `TableCell` instances, puis en ajoutant le `TableCell` et `GridViewRow` à la hiérarchie des contrôles.

Notez que le séparateur de lignes s isolés `TableCell` est mise en forme telle qu’elle s’étend sur toute la largeur du contrôle GridView, est mise en forme à l’aide de la `SortHeaderRowStyle` classe CSS et a son `Text` telle qu’elle affiche à la fois le groupe de tri nom de la propriété (par exemple, la catégorie) et la valeur du groupe s (par exemple, boissons). Enfin, `lastValue` est mis à jour à la valeur de `currentValue`.

La classe CSS utilisée pour le formatage de la ligne d’en-tête de groupe tri `SortHeaderRowStyle` doit être spécifié dans le `Styles.css` fichier. N’hésitez pas à utiliser les paramètres de style contester J’ai utilisé la commande suivante :


[!code-css[Main](creating-a-customized-sorting-user-interface-cs/samples/sample5.css)]

Avec le code en cours, l’interface de tri ajoute des en-têtes de groupe de tri lors du tri par n’importe quel BoundField (voir Figure 5, qui présente une capture d’écran lors du tri par fournisseur). Toutefois, lors du tri par tout autre type de champ (par exemple, un CheckBoxField ou de TemplateField), les en-têtes de groupe de tri sont nulle part à rechercher (voir Figure 6).


[![L’Interface de tri inclut les en-têtes de groupe de tri lors du tri par BoundFields](creating-a-customized-sorting-user-interface-cs/_static/image12.png)](creating-a-customized-sorting-user-interface-cs/_static/image11.png)

**Figure 5**: le tri Interface inclut tri groupe en-têtes lorsque le tri par BoundFields ([cliquez pour afficher l’image en taille réelle](creating-a-customized-sorting-user-interface-cs/_static/image13.png))


[![Les en-têtes de groupe de tri sont manquants lorsque le tri un CheckBoxField](creating-a-customized-sorting-user-interface-cs/_static/image15.png)](creating-a-customized-sorting-user-interface-cs/_static/image14.png)

**Figure 6**: les en-têtes de groupe de tri sont manquants lorsque le tri un CheckBoxField ([cliquez pour afficher l’image en taille réelle](creating-a-customized-sorting-user-interface-cs/_static/image16.png))


La raison pour laquelle les en-têtes de groupe de tri sont manquants lors du tri par un CheckBoxField est, car le code utilise actuellement uniquement la `TableCell` s `Text` propriété pour déterminer la valeur de la colonne triée pour chaque ligne. Pour CheckBoxFields, le `TableCell` s `Text` propriété est une chaîne vide ; au lieu de cela, la valeur est disponible via un contrôle de case à cocher Web qui se trouve dans le `TableCell` s `Controls` collection.

Pour gérer les types de champ autre que BoundFields, nous avons besoin d’augmenter le code où le `currentValue` variable est assignée pour vérifier l’existence d’une case à cocher dans la `TableCell` s `Controls` collection. Au lieu d’utiliser `currentValue = gvr.Cells[sortColumnIndex].Text`, remplacer ce code avec les éléments suivants :


[!code-csharp[Main](creating-a-customized-sorting-user-interface-cs/samples/sample6.cs)]

Ce code examine la colonne triée `TableCell` pour la ligne actuelle pour déterminer s’il existe des contrôles dans le `Controls` collection. S’il existe et que le premier contrôle est une case à cocher, le `currentValue` variable est définie sur Oui ou non, en fonction de la case à cocher s `Checked` propriété. Sinon, la valeur est obtenue à partir de la `TableCell` s `Text` propriété. Cette logique peut être répliquée pour gérer le tri pour n’importe quel TemplateField qui peut-être exister dans le GridView.

Avec l’ajout de code ci-dessus, les en-têtes de groupe de tri sont désormais présentes lors du tri par le CheckBoxField abandonnées (voir la Figure 7).


[![Les en-têtes de groupe de tri sont désormais présent lorsque le tri un CheckBoxField](creating-a-customized-sorting-user-interface-cs/_static/image18.png)](creating-a-customized-sorting-user-interface-cs/_static/image17.png)

**Figure 7**: les en-têtes de groupe de tri sont désormais présent lorsque le tri un CheckBoxField ([cliquez pour afficher l’image en taille réelle](creating-a-customized-sorting-user-interface-cs/_static/image19.png))


> [!NOTE]
> Si vous disposez de produits avec `NULL` de base de données des valeurs pour le `CategoryID`, `SupplierID`, ou `UnitPrice` champs, ces valeurs seront affichent comme des chaînes vides dans le GridView par défaut, ce qui signifie le texte de ligne s séparateur pour les produits avec `NULL`valeurs seront lues comme catégorie : (autrement dit, il n’y a s aucun nom de catégorie : comme avec la catégorie : boissons). Si vous souhaitez une valeur affichée ici vous pouvez définir le BoundFields [ `NullDisplayText` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.boundfield.nulldisplaytext.aspx) au texte que vous souhaitez afficher, ou vous pouvez ajouter une instruction conditionnelle dans la méthode de rendu lors de l’attribution du `currentValue` pour le séparateur ligne s `Text` propriété.


## <a name="summary"></a>Résumé

Le contrôle GridView n’inclut pas de nombreuses options intégrées pour la personnalisation de l’interface de tri. Toutefois, avec un peu de code de bas niveau, il s possible de modifier la hiérarchie de contrôle GridView s pour créer une interface plus personnalisée. Dans ce didacticiel, nous avons vu comment ajouter une ligne de séparateur de groupe de tri pour un GridView pouvant être trié, qui identifie les plus facilement les groupes distincts et ces limites de groupes. Pour obtenir des exemples supplémentaires d’interfaces de tri personnalisés, passez en revue [Scott Guthrie](https://weblogs.asp.net/scottgu/) s [ASP.NET 2.0 GridView tri conseils et astuces](https://weblogs.asp.net/scottgu/archive/2006/02/11/437995.aspx) entrée de blog.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](sorting-custom-paged-data-cs.md)
[Suivant](paging-and-sorting-report-data-vb.md)
