---
uid: web-forms/overview/data-access/caching-data/caching-data-with-the-objectdatasource-cs
title: "La mise en cache des données avec ObjectDataSource (c#) | Documents Microsoft"
author: rick-anderson
description: "La mise en cache peut signifier la différence entre une lente et une exécution rapide des applications Web. Ce didacticiel est la première des quatre détaillées examiner la mise en cache dans ASP.NET..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/30/2007
ms.topic: article
ms.assetid: bd87413c-8160-4520-a8a2-43b555c4183a
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/caching-data/caching-data-with-the-objectdatasource-cs
msc.type: authoredcontent
ms.openlocfilehash: 5ce0bd1d3302ee68c9c65584686172a07143e4a4
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="caching-data-with-the-objectdatasource-c"></a>La mise en cache des données avec ObjectDataSource (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_58_CS.exe) ou [télécharger le PDF](caching-data-with-the-objectdatasource-cs/_static/datatutorial58cs1.pdf)

> La mise en cache peut signifier la différence entre une lente et une exécution rapide des applications Web. Ce didacticiel est la première des quatre détaillées examiner la mise en cache dans ASP.NET. Découvrez les concepts fondamentaux de la mise en cache et comment appliquer la mise en cache à la couche de présentation via le contrôle ObjectDataSource.


## <a name="introduction"></a>Introduction

En informatique, *mise en cache* est le processus de prise de données ou des informations sont coûteuses d’obtenir et stocker une copie dans un emplacement qui est plus rapide pour l’accès. Pour les applications pilotées par les données, des requêtes volumineuses et complexes consomment généralement la majorité de la durée d’exécution de l’application s. Telle une application s, puis, pouvez souvent améliorer les performances en stockant les résultats des requêtes de base de données coûteux dans la mémoire d’application s.

ASP.NET 2.0 offre une variété d’options de mise en cache. Un ensemble de la page web ou d’un balisage s rendus de contrôle utilisateur peut être mis en cache via *mise en cache de sortie*. Les contrôles ObjectDataSource et SqlDataSource fournissent des fonctionnalités mise en cache, de même qu’en permettant à des données doit être mis en cache au niveau du contrôle. Et ASP.NET s *cache de données* fournit une API de mise en cache riche qui permet aux développeurs de page par programme des objets du cache. Dans ce didacticiel et les trois suivants, que nous allons examiner à l’aide de la s ObjectDataSource mise en cache de fonctionnalités, ainsi que le cache de données. Nous aborderons également comment mettre en cache les données de l’application au démarrage et à maintenir les données mises en cache actualisé via l’utilisation de dépendances de cache SQL. Ces didacticiels Explorer pas la mise en cache de sortie. Pour obtenir une présentation détaillée sur la mise en cache de sortie, consultez [mise en cache de sortie dans ASP.NET 2.0](http://aspnet.4guysfromrolla.com/articles/121306-1.aspx).

Mise en cache peut être appliquée en tout lieu dans l’architecture, à partir de la couche d’accès aux données de la couche présentation. Dans ce didacticiel, nous allons étudier d’appliquer la mise en cache à la couche de présentation via le contrôle ObjectDataSource. Dans le didacticiel suivant, que nous allons aborder la mise en cache les données au niveau de la couche de logique métier.

## <a name="key-caching-concepts"></a>Concepts de mise en cache de clé

La mise en cache peut améliorer considérablement les s application une évolutivité en prenant des données qui sont coûteuses générer et stocker une copie dans un emplacement qui est accessible de manière plus efficace et les performances globales. Étant donné que le cache contient une copie des données réelles, sous-jacent, il peut devenir obsolète, ou *obsolètes*, si les données sous-jacentes sont modifiées. Pour faire face à cela, un développeur de pages peut indiquer des critères par laquelle l’élément de cache sera *supprimés* à partir du cache, à l’aide :

- **Critères de temps** un élément peut-être être ajouté au cache pour absolue ou glissante durée. Par exemple, un développeur de pages peut indiquer une durée, par exemple, 60 secondes. Avec une durée absolue, l’élément mis en cache est supprimé de 60 secondes après que qu’il a été ajouté au cache, indépendamment de la fréquence à laquelle il a accédé. Avec une période glissante, l’élément mis en cache est supprimé de 60 secondes après le dernier accès.
- **Critères de dépendance** une dépendance peut être associée à un élément lors de l’ajout au cache. Lors de la modification de la dépendance de l’élément s’il est supprimé du cache. La dépendance peut être un fichier, un autre élément de cache ou une combinaison des deux. ASP.NET 2.0 permet également de dépendances de cache SQL, qui permettent aux développeurs d’ajouter un élément au cache et qu’il supprimé lorsque la base de données sous-jacente change. Nous allons examiner les dépendances de cache SQL dans les prochaines [à l’aide des dépendances de Cache SQL](using-sql-cache-dependencies-cs.md) didacticiel.

Quel que soit les critères d’éviction spécifiés peut être un élément dans le cache *au nettoyage* avant les critères basée sur une dépendance ou temps a été remplie. Si le cache a atteint sa capacité, les éléments existants doivent être supprimés avant de pouvoir ajouter des nouveaux. Par conséquent, lorsque par programmation l’utilisation avec les données mises en cache s vital que vous supposez toujours que les données mises en cache ne peuvent pas être présentes. Nous allons examiner le modèle à utiliser lors de l’accès aux données à partir du cache par programmation dans notre didacticiel suivant, *mise en cache des données dans l’Architecture*.

La mise en cache permet d’économique tirant plus de performances à partir d’une application. En tant que [Steven Smith](http://aspadvice.com/blogs/ssmith/) énonce dans son article [mise en cache ASP.NET : Techniques et meilleures pratiques](https://msdn.microsoft.com/en-us/library/aa478965.aspx):

La mise en cache peut être un bon moyen pour obtenir une bonne suffisamment les performances sans nécessiter beaucoup de temps et d’analyse. La mémoire est bon marchée, si vous pouvez obtenir des performances en mettant en cache la sortie de 30 secondes au lieu de perdre un jour ou semaine de l’optimisation de votre code ou la base de données, effectuez la solution de mise en cache (en supposant que 30 - seconde anciennes données est OK) et passer. Par la suite, une mauvaise conception accèdera probablement, donc bien sûr vous devriez concevoir correctement vos applications. Mais si vous devez obtenir une bonne suffisamment performances aujourd'hui, la mise en cache peut être une excellente [approche], l’achat du temps de refactoriser votre application à une date ultérieure, lorsque vous avez le temps de le faire.

Lors de la mise en cache peut fournir des améliorations de performances sensible, il n’est pas applicable dans tous les cas, comme avec les applications qui utilisent des données fréquemment mise à jour en temps réel, ou lorsque la même peu-durée de vie des données obsolètes sont inacceptables. Mais, pour la plupart des applications, la mise en cache doit être utilisé. Pour plus d’informations sur la mise en cache dans ASP.NET 2.0, reportez-vous à la [mise en cache pour des performances](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/caching/default.aspx) section de la [didacticiels de démarrage rapide ASP.NET 2.0](https://quickstarts.asp.net/QuickStartv20/aspnet/).

## <a name="step-1-creating-the-caching-web-pages"></a>Étape 1 : Création de Pages Web mise en cache

Avant de commencer à notre exploration des fonctionnalités de mise en cache de s ObjectDataSource, permettent de s tout d’abord prendre quelques instants pour créer les pages ASP.NET dans notre projet de site Web que nous aurons besoin pour ce didacticiel et les trois suivants. Commencez par ajouter un nouveau dossier nommé `Caching`. Ensuite, ajoutez les pages ASP.NET suivantes à ce dossier, en veillant à associer chaque page avec le `Site.master` page maître :

- `Default.aspx`
- `ObjectDataSource.aspx`
- `FromTheArchitecture.aspx`
- `AtApplicationStartup.aspx`
- `SqlCacheDependencies.aspx`


![Ajouter les Pages ASP.NET pour les didacticiels relatifs à la mise en cache](caching-data-with-the-objectdatasource-cs/_static/image1.png)

**Figure 1**: ajouter les Pages ASP.NET pour les didacticiels relatifs à la mise en cache


Comme dans les autres dossiers, `Default.aspx` dans le `Caching` dossier répertorie les didacticiels dans sa section. N’oubliez pas que le `SectionLevelTutorialListing.ascx` contrôle utilisateur fournit cette fonctionnalité. Par conséquent, ajoutez ce contrôle utilisateur pour `Default.aspx` en le faisant glisser à partir de l’Explorateur de solutions sur la page s mode Création.


[![Figure 2 : Ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx](caching-data-with-the-objectdatasource-cs/_static/image3.png)](caching-data-with-the-objectdatasource-cs/_static/image2.png)

**Figure 2**: Figure 2 : ajouter la `SectionLevelTutorialListing.ascx` contrôle utilisateur à `Default.aspx` ([cliquez pour afficher l’image en taille réelle](caching-data-with-the-objectdatasource-cs/_static/image4.png))


Enfin, ajoutez ces pages en tant qu’entrées pour les `Web.sitemap` fichier. En particulier, ajoutez le balisage suivant après l’utilisation des données binaires `<siteMapNode>`:


[!code-xml[Main](caching-data-with-the-objectdatasource-cs/samples/sample1.xml)]

Après la mise à jour `Web.sitemap`, prenez un moment pour afficher le site Web des didacticiels via un navigateur. Le menu de gauche inclut désormais les éléments pour les didacticiels de mise en cache.


![Le plan de Site inclut maintenant des entrées pour les didacticiels de mise en cache](caching-data-with-the-objectdatasource-cs/_static/image5.png)

**Figure 3**: le plan de Site inclut maintenant des entrées pour les didacticiels de mise en cache


## <a name="step-2-displaying-a-list-of-products-in-a-web-page"></a>Étape 2 : Afficher une liste de produits dans une Page Web

Ce didacticiel explique comment utiliser les ObjectDataSource contrôle s mise en cache des fonctionnalités intégrées. Avant de nous pouvons examiner ces fonctionnalités, cependant, vous devez tout d’abord travailler à partir d’une page. S permettent de créer une page web qui utilise un contrôle GridView pour afficher des informations produit récupérées par un ObjectDataSource à partir de la `ProductsBLL` classe.

Commencez par ouvrir le `ObjectDataSource.aspx` page dans le `Caching` dossier. Faites glisser un contrôle GridView à partir de la boîte à outils vers le concepteur, définissez son `ID` propriété `Products`et, à partir de sa balise active, choisissez de lier à un nouveau contrôle ObjectDataSource nommé `ProductsDataSource`. Configurer l’ObjectDataSource pour travailler avec la `ProductsBLL` classe.


[![Configurer pour utiliser la classe ProductsBLL ObjectDataSource](caching-data-with-the-objectdatasource-cs/_static/image7.png)](caching-data-with-the-objectdatasource-cs/_static/image6.png)

**Figure 4**: configurer l’ObjectDataSource à utiliser le `ProductsBLL` classe ([cliquez pour afficher l’image en taille réelle](caching-data-with-the-objectdatasource-cs/_static/image8.png))


De cette page, permettent de créer un GridView modifiable afin que nous pouvons examiner ce qui se passe lorsque les données mises en cache dans ObjectDataSource sont modifiées via l’interface de s GridView s. Laissez la liste déroulante dans l’onglet Sélection par défaut, la valeur `GetProducts()`, mais modifier l’élément sélectionné dans l’onglet mise à jour pour le `UpdateProduct` surcharge qui accepte `productName`, `unitPrice`, et `productID` comme paramètres d’entrée.


[![La valeur de la liste déroulante de mise à jour onglet s la surcharge appropriée UpdateProduct](caching-data-with-the-objectdatasource-cs/_static/image10.png)](caching-data-with-the-objectdatasource-cs/_static/image9.png)

**Figure 5**: la valeur de la liste déroulante de s onglet mettre à jour le convenant `UpdateProduct` de surcharge ([cliquez pour afficher l’image en taille réelle](caching-data-with-the-objectdatasource-cs/_static/image11.png))


Enfin, définissez les listes déroulantes dans les onglets INSERT et DELETE (aucun) et cliquez sur Terminer. À la fin de l’Assistant Configurer la Source de données, Visual Studio définit les opérations de mappage ObjectDataSource `OldValuesParameterFormatString` propriété `original_{0}`. Comme indiqué dans le [une vue d’ensemble d’insertion, de mise à jour et de suppression des données](../editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-cs.md) (didacticiel), cette propriété doit être supprimé de la syntaxe déclarative ou redéfinis à sa valeur par défaut, `{0}`, dans l’ordre du processus de mise à jour continuer sans erreur.

En outre, à l’issue de l’Assistant Visual Studio ajoute un champ pour le contrôle GridView pour chacun des champs de données de produit. Supprimer tout sauf la `ProductName`, `CategoryName`, et `UnitPrice` BoundFields. Ensuite, mettez à jour le `HeaderText` propriétés de chacune de ces BoundFields de produits, de catégorie et de prix, respectivement. Étant donné que la `ProductName` champ est obligatoire, convertir le BoundField en TemplateField et ajoutez un contrôle RequiredFieldValidator à la `EditItemTemplate`. De même, convertir les `UnitPrice` BoundField en TemplateField et ajoutez un CompareValidator pour vous assurer que la valeur entrée par l’utilisateur est une devise valide valeur s supérieure ou égale à zéro. En plus de ces modifications, n’hésitez pas à effectuer toutes les modifications esthétiques, telles que l’alignement à droite la `UnitPrice` valeur ou la spécification de la mise en forme pour la `UnitPrice` texte dans ses interfaces en lecture seule et d’édition.

Modifier le contrôle GridView en activant la case à cocher Activer la modification dans la balise active de GridView s. Vérifiez également les cases à cocher Activer la pagination et activer le tri.

> [!NOTE]
> Besoin d’une revue de la personnalisation de l’interface de modification GridView s ? Dans ce cas, faire référence à la [personnalisation de l’Interface de Modification de données](../editing-inserting-and-deleting-data/customizing-the-data-modification-interface-cs.md) didacticiel.


[![Activer la prise en charge du GridView pour modification, de tri et de pagination](caching-data-with-the-objectdatasource-cs/_static/image13.png)](caching-data-with-the-objectdatasource-cs/_static/image12.png)

**Figure 6**: activer la prise en charge GridView pour modification, le tri et la pagination ([cliquez pour afficher l’image en taille réelle](caching-data-with-the-objectdatasource-cs/_static/image14.png))


Après avoir apporté ces modifications GridView, le balisage déclaratif s GridView et ObjectDataSource doit ressembler à ce qui suit :


[!code-aspx[Main](caching-data-with-the-objectdatasource-cs/samples/sample2.aspx)]

Comme le montre la Figure 7, le contrôle GridView modifiable répertorie le nom, la catégorie et le prix de chacun des produits dans la base de données. Prenez un moment pour tester le tri de la fonctionnalité de page s les résultats, les, parcourir et modifier un enregistrement.


[![Chaque nom de produit, la catégorie et le prix sont répertorié dans un triable, Pageable GridView modifiable](caching-data-with-the-objectdatasource-cs/_static/image16.png)](caching-data-with-the-objectdatasource-cs/_static/image15.png)

**Figure 7**: chaque produit s nom, catégorie et prix est répertorié dans un triable, Pageable GridView modifiable ([cliquez pour afficher l’image en taille réelle](caching-data-with-the-objectdatasource-cs/_static/image17.png))


## <a name="step-3-examining-when-the-objectdatasource-is-requesting-data"></a>Étape 3 : Examen lorsque ObjectDataSource l’est demande des données

Le `Products` GridView récupère ses données à afficher en appelant le `Select` méthode de la `ProductsDataSource` ObjectDataSource. Cette ObjectDataSource crée une instance de la couche de logique métier s `ProductsBLL` classe et appelle son `GetProducts()` (méthode), qui à son tour appelle la couche d’accès aux données s `ProductsTableAdapter` s `GetProducts()` (méthode). La méthode de la couche DAL se connecte à la base de données Northwind et émet configuré `SELECT` requête. Ces données sont ensuite retournées à la couche DAL, qui empaquette dans un `NorthwindDataTable`. L’objet DataTable est retournée à la couche BLL, qui renvoie à ObjectDataSource, ce qui renvoie au GridView. Le contrôle GridView crée ensuite un `GridViewRow` objet pour chaque `DataRow` dans la table de données et chaque `GridViewRow` est restitué par la suite dans le code HTML qui est retourné au client et affiché sur le navigateur du visiteur s.

Cette séquence d’événements se produit chaque fois que le contrôle GridView doit se lier à ses données sous-jacentes. Cela se produit lorsque la page est visitée en premier, lors du déplacement d’une page de données à un autre, lors du tri GridView, ou lorsque vous modifiez les données de s GridView via son intégré, modifier ou supprimer des interfaces. Si l’état d’affichage s GridView est désactivée, le contrôle GridView est reliée sur chaque publication (postback) ainsi. Le contrôle GridView peut également être explicitement reliée à ses données en appelant son `DataBind()` (méthode).

Pour apprécier pleinement la fréquence à laquelle les données sont récupérées à partir de la base de données, permettent d’afficher un message indiquant le moment où les données sont ré-récupérées s. Ajouter un contrôle Web Label ci-dessus le GridView nommé `ODSEvents`. Effacer son `Text` propriété et définissez son `EnableViewState` propriété `false`. Sous l’étiquette, ajouter un contrôle bouton et définissez ses `Text` propriété à une publication (postback).


[![Ajouter une étiquette et un bouton à la Page au-dessus du contrôle GridView](caching-data-with-the-objectdatasource-cs/_static/image19.png)](caching-data-with-the-objectdatasource-cs/_static/image18.png)

**Figure 8**: ajouter une étiquette et un bouton à la Page au-dessus de GridView ([cliquez pour afficher l’image en taille réelle](caching-data-with-the-objectdatasource-cs/_static/image20.png))


Pendant le flux de travail de l’accès du données, le s ObjectDataSource `Selecting` événement se déclenche avant la création de l’objet sous-jacent et sa méthode configuré appelé. Créer un gestionnaire d’événements pour cet événement et ajoutez le code suivant :


[!code-csharp[Main](caching-data-with-the-objectdatasource-cs/samples/sample3.cs)]

L’étiquette affiche chaque fois Qu'objectdatasource effectue une demande à l’architecture pour les données, l’événement de sélection de texte déclenché.

Visitez cette page dans un navigateur. Lorsque la page est visitée en premier, l’événement de sélection de texte déclenché est indiqué. Cliquez sur le bouton de publication (postback) et notez que le texte disparaît (en supposant que le contrôle GridView s `EnableViewState` est définie sur `true`, la valeur par défaut). Il s’agit, car, lors de la publication, le contrôle GridView est reconstruit à partir de son état d’affichage et par conséquent ne nous tourner vers ObjectDataSource pour ses données. Tri, la pagination ou la modification des données, cependant, fait le contrôle GridView à relier à sa source de données, et par conséquent, l’événement Selecting activés dans le texte s’affiche de nouveau.


[![Chaque fois que le contrôle GridView est reliée à sa Source de données, si vous sélectionnez déclenché d’événement s’affiche.](caching-data-with-the-objectdatasource-cs/_static/image22.png)](caching-data-with-the-objectdatasource-cs/_static/image21.png)

**Figure 9**: chaque fois que GridView est reliée à sa Source de données, de déclenchement d’événement si vous sélectionnez s’affiche ([cliquez pour afficher l’image en taille réelle](caching-data-with-the-objectdatasource-cs/_static/image23.png))


[![Cliquez sur le bouton déclenche la publication (postback) le contrôle GridView à être reconstruites à partir de son état d’affichage](caching-data-with-the-objectdatasource-cs/_static/image25.png)](caching-data-with-the-objectdatasource-cs/_static/image24.png)

**La figure 10**: en cliquant sur le bouton de publication (postback) entraîne le contrôle GridView à être reconstruites à partir de son état d’affichage ([cliquez pour afficher l’image en taille réelle](caching-data-with-the-objectdatasource-cs/_static/image26.png))


Il a beau sembler inutile récupérer les données de la base de données chaque fois que les données sont paginées via ou triées. Après tout, dans la mesure où re à l’aide de la pagination par défaut, ObjectDataSource a extraire tous les enregistrements lors de l’affichage de la première page. Même si le contrôle GridView ne fournit pas de tri et de pagination de prise en charge, les données doivent être récupérées à partir de la base de données chaque fois que la page est visitée en premier par n’importe quel utilisateur (et à chaque publication, si l’état d’affichage est désactivé). Mais si le contrôle GridView ne s’affichent les mêmes données à tous les utilisateurs, ces demandes de base de données supplémentaires sont superflues. Pourquoi ne pas mettre en cache les résultats retournés à partir de la `GetProducts()` (méthode) et lier le contrôle GridView à ceux mis en cache les résultats ?

## <a name="step-4-caching-the-data-using-the-objectdatasource"></a>Étape 4 : Mise en cache les données à l’aide de ObjectDataSource

En définissant simplement quelques propriétés, ObjectDataSource peut être configuré pour mettre en cache automatiquement ses données récupérées dans le cache de données ASP.NET. La liste suivante résume les propriétés liés au cache de ObjectDataSource :

- [EnableCaching](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.objectdatasource.enablecaching.aspx) doit avoir la valeur `true` pour activer la mise en cache. La valeur par défaut est `false`.
- [CacheDuration](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.objectdatasource.cacheduration.aspx) la quantité de temps, en secondes, pendant laquelle les données sont mises en cache. La valeur par défaut est 0. ObjectDataSource stockera uniquement les données si `EnableCaching` est `true` et `CacheDuration` est définie sur une valeur supérieure à zéro.
- [CacheExpirationPolicy](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.objectdatasource.cacheexpirationpolicy.aspx) peut être définie sur `Absolute` ou `Sliding`. Si `Absolute`, ObjectDataSource met en cache les données récupérées pour `CacheDuration` secondes ; si `Sliding`, les données expirent uniquement une fois qu’il n’a pas été accédé pour `CacheDuration` secondes. La valeur par défaut est `Absolute`.
- [CacheKeyDependency](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.objectdatasource.cachekeydependency.aspx) cette propriété permet d’associer les entrées de cache ObjectDataSource s avec une dépendance de cache existante. Les entrées de données s ObjectDataSource peuvent être prématurément supprimées du cache en faisant expirer qui lui est associée `CacheKeyDependency`. Cette propriété est couramment utilisée pour associer une dépendance de cache SQL avec le cache de s ObjectDataSource, une rubrique nous allons Explorer à l’avenir [à l’aide des dépendances de Cache SQL](using-sql-cache-dependencies-cs.md) didacticiel.

S permettent de configurer le `ProductsDataSource` ObjectDataSource à ses données en cache pendant 30 secondes sur une échelle absolue. Définir le s ObjectDataSource `EnableCaching` propriété `true` et son `CacheDuration` propriété à 30. Laissez le `CacheExpirationPolicy` sa valeur par défaut, la valeur de propriété `Absolute`.


[![Configurer l’ObjectDataSource pour mettre en Cache ses données pour les 30 secondes](caching-data-with-the-objectdatasource-cs/_static/image28.png)](caching-data-with-the-objectdatasource-cs/_static/image27.png)

**Figure 11**: configurer l’ObjectDataSource pour mettre en Cache ses données pendant 30 secondes ([cliquez pour afficher l’image en taille réelle](caching-data-with-the-objectdatasource-cs/_static/image29.png))


Enregistrez vos modifications et revisiter cette page dans un navigateur. La déclenchement d’événement de sélection de texte s’affiche lorsque vous visitez tout d’abord la page, comme initialement les données ne sont pas dans le cache. Mais les publications ultérieures déclenchées en cliquant sur le bouton de publication (postback), le tri, la pagination, ou en cliquant sur le bouton Modifier ou annuler *pas* actualiser l’événement Selecting a déclenché le texte. C’est parce que le `Selecting` événement se déclenche uniquement lorsque le ObjectDataSource obtient ses données à partir de son objet sous-jacent ; le `Selecting` ne se déclenche pas si les données sont extraites à partir du cache de données.

Les données seront supprimées après 30 secondes, à partir du cache. Les données seront également supprimées du cache si le s ObjectDataSource `Insert`, `Update`, ou `Delete` les méthodes sont appelées. Par conséquent, après 30 secondes ou le bouton de mise à jour a été activé, le tri, la pagination, ou en cliquant sur le bouton Modifier ou Annuler entraîne l’ObjectDataSource obtenir ses données à partir de son objet sous-jacent, affichage de l’événement Selecting déclenché texte lors de la `Selecting` se déclenche des événements. Les résultats retournés sont placés dans le cache de données.

> [!NOTE]
> Si vous voyez fréquemment la déclenchement d’événement de sélection de texte, même lorsque vous pensez ObjectDataSource de travailler avec des données mises en cache, il peut être dû à des contraintes de mémoire. S’il n’est pas suffisamment de mémoire disponible, les données ajoutées au cache par ObjectDataSource peuvent avoir été récupérées. Si t ObjectDataSource ne semble être correctement la mise en cache les données ou les caches uniquement les données sporadique, fermez certaines applications pour libérer de la mémoire, puis réessayez.


Figure 12 illustre le s ObjectDataSource mise en cache de flux de travail. Lors du déclenche de l’événement Selecting texte s’affiche sur votre écran, c’est parce que les données n’était pas dans le cache et devaient être récupérés à partir de l’objet sous-jacent. Lorsque ce texte est manquant, toutefois, il s, car les données étaient disponibles à partir du cache. Lorsque les données sont retournées à partir du cache là s aucun appel à l’objet sous-jacent et, par conséquent, aucune requête de base de données exécutées.


![Les magasins de ObjectDataSource et les récupère ses données à partir du Cache de données](caching-data-with-the-objectdatasource-cs/_static/image30.png)

**Figure 12**: ObjectDataSource stocke et récupère ses données à partir du Cache de données


Chaque application ASP.NET a son propre cache de données d’instance s partagées par toutes les pages et les visiteurs. Cela signifie que les données stockées dans le cache de données par ObjectDataSource sont également partagées entre tous les utilisateurs visitant la page. Pour vérifier cela, ouvrez le `ObjectDataSource.aspx` page dans un navigateur. Lors de la première visite de la page, la déclenchement d’événement de sélection de texte s’affiche (en supposant que les données ajoutées au cache par les tests précédents a, à ce stade, été supprimées). Ouvrez une deuxième instance du navigateur et la copie et collez l’URL de la première instance du navigateur à la seconde. Dans la deuxième instance du navigateur, la déclenchement d’événement de sélection de texte n’est pas affichée car il s à l’aide de la même mis en cache les données en tant que la première.

Lors de l’insertion de ses données récupérées dans le cache, ObjectDataSource utilise une valeur de clé de cache qui inclut : le `CacheDuration` et `CacheExpirationPolicy` les valeurs de propriété ; le type de l’objet métier sous-jacente utilisée par ObjectDataSource, qui est spécifiée via le [ `TypeName` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.objectdatasource.typename.aspx) (`ProductsBLL`, dans cet exemple) ; la valeur de la `SelectMethod` propriété et le nom et les valeurs des paramètres dans le `SelectParameters` collection ; et les valeurs de ses `StartRowIndex`et `MaximumRows` propriétés, qui sont utilisées lors de l’implémentation [la pagination personnalisée.](../paging-and-sorting/paging-and-sorting-report-data-cs.md)

Élaboration de la valeur de clé de cache comme une combinaison de ces propriétés permet de garantir une entrée de cache unique comme ces valeurs changent. Par exemple, dans ces didacticiels nous ve examiné à l’aide de la `ProductsBLL` classe s `GetProductsByCategoryID(categoryID)`, qui retourne tous les produits pour une catégorie spécifiée. Un utilisateur peut provenir des page et afficher les boissons, qui a un `CategoryID` de 1. Si l’ObjectDataSource mis en cache ses résultats sans tenir compte de le `SelectParameters` de valeurs, lorsqu’un autre utilisateur a accédé à la page pour afficher les condiments pendant les produits de boissons dans le cache, ils d voient les produits de boissons mis en cache plutôt que de condiments. En modifiant la clé de cache par ces propriétés, qui incluent les valeurs de la `SelectParameters`, ObjectDataSource tient à jour une entrée de cache distinct pour beverages et condiments.

## <a name="stale-data-concerns"></a>Problèmes de données obsolètes

ObjectDataSource supprime automatiquement ses éléments à partir du cache lorsque l’un de ses `Insert`, `Update`, ou `Delete` méthodes est appelée. Cela permet de protéger les données obsolètes en supprimant les entrées du cache lorsque les données sont modifiées via la page. Toutefois, il est possible pour un ObjectDataSource à l’aide de la mise en cache pour toujours afficher des données obsolètes. Dans le cas le plus simple, il peut être dû aux données en modifiant directement dans la base de données. Par exemple un administrateur de base de données venez d’exécuter un script qui modifie certains enregistrements dans la base de données.

Ce scénario pourrait également dérouler de façon plus subtile. Alors que ObjectDataSource supprime ses éléments à partir du cache lorsque l’une de ses méthodes de modification de données est appelée, les éléments du cache supprimées sont pour l’ObjectDataSource s combinaison de valeurs de propriété (`CacheDuration`, `TypeName`, `SelectMethod`, et ainsi de suite). Si vous avez deux ObjectDataSources qui utilisent différents `SelectMethods` ou `SelectParameters`, mais peut mettre à jour les mêmes données, puis un ObjectDataSource peut mettre à jour une ligne et invalider ses propres entrées de cache, mais la ligne correspondante pour le deuxième ObjectDataSource sera toujours pris en charge à partir du cache. Nous vous invitons à créer des pages pour exposer cette fonctionnalité. Créer une page qui affiche un GridView modifiable qui extrait les données à partir d’un ObjectDataSource qui utilise la mise en cache et est configuré pour obtenir des données à partir de la `ProductsBLL` classe s `GetProducts()` (méthode). Ajoutez un autre modifiable GridView et ObjectDataSource vers cette page (ou un autre), mais pour ce deuxième ObjectDataSource soit utiliser le `GetProductsByCategoryID(categoryID)` (méthode). Depuis les deux ObjectDataSources `SelectMethod` propriétés diffèrent, ils ll chaque ont leurs propres valeurs mises en cache. Si vous modifiez un produit dans une grille, la prochaine fois que vous liez les données à la grille des autres (par la pagination, le tri, etc.), il sera encore servir les données anciennes, mis en cache et ne reflète pas la modification a été effectuée à partir de la grille des autres.

En bref, utilisent uniquement temporels des expirations dans le si vous êtes disposé à ont la possibilité de données périmées et utiliser des expirations dans le plus courtes pour les scénarios où l’actualisation des données est importante. Si des données obsolètes ne sont pas acceptables, renoncer à la mise en cache ou utiliser les dépendances de cache SQL (en supposant qu’il soit des données de base de données vous re mise en cache). Nous allons explorer les dépendances de cache SQL dans un didacticiel futures.

## <a name="summary"></a>Résumé

Dans ce didacticiel, nous avons examiné les fonctionnalités de mise en cache intégrées ObjectDataSource s. En définissant simplement quelques propriétés, nous pouvons indiquer ObjectDataSource pour mettre en cache les résultats retournés à partir du spécifié `SelectMethod` dans le cache de données ASP.NET. Le `CacheDuration` et `CacheExpirationPolicy` propriétés indiquent la durée de l’élément est mis en cache et si elle est absolue ou glissante d’expiration. Le `CacheKeyDependency` propriété associe toutes les entrées de cache ObjectDataSource s avec une dépendance de cache existante. Cela permet de supprimer les entrées s ObjectDataSource le cache avant l’expiration de la durée est atteinte et est généralement utilisée avec les dépendances de cache SQL.

Étant donné que ObjectDataSource met simplement en cache ses valeurs dans le cache de données, nous avons répliquer la fonctionnalité intégrée de s ObjectDataSource par programme. Il ne les sens pour ce faire au niveau de la couche présentation, étant donné que ObjectDataSource offre cette fonctionnalité prêtes à l’emploi, mais nous pouvons implémenter des fonctionnalités de mise en cache dans une couche distincte de l’architecture. Pour ce faire, nous devrons répéter la même logique utilisée par ObjectDataSource. Nous allons observer comment programmer avec le cache de données à partir de l’architecture dans notre didacticiel suivant.

Bonne programmation !

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Mise en cache ASP.NET : Meilleures pratiques et les Techniques](https://msdn.microsoft.com/en-us/library/aa478965.aspx)
- [Guide d’Architecture de mise en cache pour les Applications .NET Framework](https://msdn.microsoft.com/en-us/library/ee817645.aspx)
- [Mise en cache de sortie dans ASP.NET 2.0](http://aspnet.4guysfromrolla.com/articles/121306-1.aspx)

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Teresa Murphy. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Next](caching-data-in-the-architecture-cs.md)
