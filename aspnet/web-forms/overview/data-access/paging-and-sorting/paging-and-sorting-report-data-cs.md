---
uid: web-forms/overview/data-access/paging-and-sorting/paging-and-sorting-report-data-cs
title: "Signalent la pagination et le tri des données (c#) | Documents Microsoft"
author: rick-anderson
description: "La pagination et le tri sont deux fonctionnalités très courantes lors de l’affichage des données dans une application en ligne. Dans ce didacticiel, nous allons examiner un ajout du tri et..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/15/2006
ms.topic: article
ms.assetid: 811a6ef2-ec66-4c8e-a089-6f795056e288
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/paging-and-sorting/paging-and-sorting-report-data-cs
msc.type: authoredcontent
ms.openlocfilehash: fd365ca3ae8e832e368fa4c29c33af8a42cf41d2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="paging-and-sorting-report-data-c"></a>La pagination et le tri des données de rapport (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_24_CS.exe) ou [télécharger le PDF](paging-and-sorting-report-data-cs/_static/datatutorial24cs1.pdf)

> La pagination et le tri sont deux fonctionnalités très courantes lors de l’affichage des données dans une application en ligne. Dans ce didacticiel, nous allons examiner un ajout de tri et de pagination à vos rapports, vous allez générer ensuite à l’avenir les didacticiels.


## <a name="introduction"></a>Introduction

La pagination et le tri sont deux fonctionnalités très courantes lors de l’affichage des données dans une application en ligne. Par exemple, lorsque vous recherchez dans la documentation d’ASP.NET sur une librairie en ligne, il peut y avoir des centaines de cette documentation, mais le rapport qui répertorie les résultats de recherche répertorie seulement dix correspondances par page. En outre, les résultats peuvent être triés par titre, prix, nombre de pages, nom de l’auteur et ainsi de suite. Alors que le passé 23 didacticiels ont examiné comment créer une variété de rapports, notamment les interfaces qui permettent l’ajout, la modification et suppression de données, nous ve ne pas examiné comment trier les données et le seul échange exemples nous ve vu ont été avec le contrôle DetailsView et FormView contrôles.

Dans ce didacticiel, nous verrons comment ajouter de tri et de pagination aux rapports, ce qui peuvent être effectués en vérifiant simplement les quelques cases. Malheureusement, cette implémentation simpliste a ses inconvénients de que l’interface de tri laisse un bit à être souhaité et les routines de pagination ne sont pas conçus pour efficacement la pagination des jeux de résultats volumineux. Didacticiels futures vous apprendrez à surmonter les limitations de l’out-of-du-box pagination et le tri des solutions.

## <a name="step-1-adding-the-paging-and-sorting-tutorial-web-pages"></a>Étape 1 : Ajout de la pagination et le tri des Pages Web d’un didacticiel

Avant de commencer ce didacticiel, permettent de s Prenez tout d’abord ajouter les pages ASP.NET que nous aurons besoin pour ce didacticiel et les trois suivants. Commencez par créer un nouveau dossier dans le projet nommé `PagingAndSorting`. Ensuite, ajoutez les pages ASP.NET cinq suivantes à ce dossier, configuré pour utiliser la page maître de `Site.master`:

- `Default.aspx`
- `SimplePagingSorting.aspx`
- `EfficientPaging.aspx`
- `SortParameter.aspx`
- `CustomSortingUI.aspx`


![Créez un dossier PagingAndSorting et ajouter les Pages ASP.NET didacticiel](paging-and-sorting-report-data-cs/_static/image1.png)

**Figure 1**: créez un dossier PagingAndSorting et ajouter les Pages ASP.NET didacticiel


Ensuite, ouvrez le `Default.aspx` page et faites glisser le `SectionLevelTutorialListing.ascx` contrôle utilisateur à partir de la `UserControls` dossier sur l’aire de conception. Ce contrôle utilisateur, que nous avons créé dans le [les Pages maîtres et Navigation du Site](../introduction/master-pages-and-site-navigation-cs.md) didacticiel, énumère le plan de site et affiche ces didacticiels dans la section en cours dans une liste à puces.


![Ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx](paging-and-sorting-report-data-cs/_static/image2.png)

**Figure 2**: ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx


Pour disposer d’afficher la pagination et le tri des didacticiels, que nous allons créer la liste à puces, nous devons les ajouter à la carte de site. Ouvrez le `Web.sitemap` et ajoutez le balisage suivant après la balise de nœud de carte site modification, d’insertion et de suppression :


[!code-xml[Main](paging-and-sorting-report-data-cs/samples/sample1.xml)]


![Mettre à jour le plan de Site pour inclure les nouvelles Pages ASP.NET](paging-and-sorting-report-data-cs/_static/image3.png)

**Figure 3**: mettre à jour le plan de Site pour inclure les nouvelles Pages ASP.NET


## <a name="step-2-displaying-product-information-in-a-gridview"></a>Étape 2 : Afficher des informations de produit dans un contrôle GridView

Avant de nous implémenter réellement la pagination et des fonctionnalités de tri, permettent de s tout d’abord créer un standard non-srotable, non paginable GridView qui affiche les informations de produit. Il s’agit d’une tâche nous ve fait plusieurs fois avant tout au long de cette série de didacticiels, par conséquent, ces étapes doit être familière. Commencez par ouvrir le `SimplePagingSorting.aspx` page et faites glisser un contrôle GridView à partir de la boîte à outils vers le concepteur, en définissant ses `ID` propriété `Products`. Ensuite, créez un nouveau ObjectDataSource qui utilise la classe ProductsBLL s `GetProducts()` méthode pour retourner toutes les informations de produit.


![Récupérer des informations sur tous les produits à l’aide de la méthode GetProducts()](paging-and-sorting-report-data-cs/_static/image4.png)

**Figure 4**: récupérer des informations sur tous les produits à l’aide de la méthode GetProducts()


Étant donné que ce rapport est un rapport en lecture seule, un s’il n’y a pas besoin mapper le s ObjectDataSource `Insert()`, `Update()`, ou `Delete()` méthodes correspondant `ProductsBLL` méthodes ; par conséquent, choisissez (aucun) dans la liste déroulante pour la mise à jour, insérer, et supprimer les tabulations.


![Choisissez (aucun) Option dans la liste déroulante dans la mise à jour, insérer et supprimer des onglets](paging-and-sorting-report-data-cs/_static/image5.png)

**Figure 5**: choisissez (aucun) Option dans la liste déroulante dans la mise à jour, insérer et supprimer des onglets


Ensuite, permettent de s personnaliser les champs de s GridView afin qu’uniquement les noms de produits, fournisseurs, catégories, prix et états supprimées sont affichés. En outre, n’hésitez effectuer la mise en forme au niveau du champ change, notamment ajuster le `HeaderText` de propriétés ou de mise en forme le prix sous forme de devise. Après ces modifications, votre balisage déclaratif de GridView s doit ressembler à ce qui suit :


[!code-aspx[Main](paging-and-sorting-report-data-cs/samples/sample2.aspx)]

La figure 6 illustre notre progression jusque-là lorsqu’ils sont affichés via un navigateur. Notez que la page répertorie tous les produits dans un seul écran, en affichant chaque nom de produit s, catégorie, fournisseur, les prix et abandonné l’état.


[![Chacun des produits répertoriés](paging-and-sorting-report-data-cs/_static/image7.png)](paging-and-sorting-report-data-cs/_static/image6.png)

**Figure 6**: chacun des produits répertoriés ([cliquez pour afficher l’image en taille réelle](paging-and-sorting-report-data-cs/_static/image8.png))


## <a name="step-3-adding-paging-support"></a>Étape 3 : Ajout de prise en charge de la pagination

Affichage de la liste *tous les* des produits sur un seul écran peut entraîner la surcharge d’informations pour l’utilisateur qui lit les données. Pour aider à rendre les résultats plus faciles à gérer, nous pouvons diviser les données dans les pages de données plus petits et autoriser l’utilisateur à parcourir les données d’une page à la fois. Pour accomplir cela Cochez simplement la case à cocher Activer la pagination de la balise active de GridView s (Cela définit le GridView s [ `AllowPaging` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.allowpaging.aspx) à `true`).


[![La case Activer la pagination pour prendre en charge la pagination](paging-and-sorting-report-data-cs/_static/image10.png)](paging-and-sorting-report-data-cs/_static/image9.png)

**Figure 7**: case à cocher Activer la pagination pour ajouter la prise en charge la pagination ([cliquez pour afficher l’image en taille réelle](paging-and-sorting-report-data-cs/_static/image11.png))


L’activation de la pagination limite le nombre d’enregistrements affichés par page et ajoute un *interface pagination* au GridView. L’interface de pagination par défaut, illustré dans la Figure 7, est une série de numéros de page, permettant à l’utilisateur accéder rapidement à partir d’une page de données à un autre. Cette interface de pagination doit sembler familière, comme nous avons vu lors de l’ajout de prise en charge la pagination pour les contrôles DetailsView et FormView dans ces didacticiels.

Contrôles à la fois le contrôle DetailsView et FormView affichent uniquement un seul enregistrement par page. Le GridView, toutefois, consulte sa [ `PageSize` propriété](https://msdn.microsoft.com/en-US/library/system.web.ui.webcontrols.gridview.pagesize.aspx) pour déterminer le nombre d’enregistrements à afficher par page (cette propriété par défaut est une valeur de 10).

Cette interface de pagination s GridView, DetailsView et FormView peut être personnalisée en utilisant les propriétés suivantes :

- `PagerStyle`Indique les informations de style pour l’interface de l’échange ; peut spécifier de paramètres tels que `BackColor`, `ForeColor`, `CssClass`, `HorizontalAlign`, et ainsi de suite.
- `PagerSettings`contient une multitude de propriétés que vous pouvez personnaliser les fonctionnalités de l’interface de l’échange ; `PageButtonCount` indique le nombre maximal de nombres de pages numériques affichées dans l’interface de pagination (la valeur par défaut est 10) ; le [ `Mode` propriété](https://msdn.microsoft.com/en-US/library/system.web.ui.webcontrols.pagersettings.mode.aspx) indique comment l’interface de pagination fonctionne et peut être défini sur : 

    - `NextPrevious`montre un boutons suivant et précédent, permettant à l’utilisateur à l’étape avancer ou reculer une page à la fois
    - `NextPreviousFirstLast`en plus des boutons suivant et précédent, premier et dernier boutons sont également inclus, autoriser l’utilisateur à accéder rapidement à la première ou dernière page de données
    - `Numeric`affiche une série de numéros de page, permettant à l’utilisateur à passer immédiatement à une page
    - `NumericFirstLast`Outre les numéros de page, comprend des boutons première et dernière, autoriser l’utilisateur à accéder rapidement à la première ou dernière page de données ; les premier/dernier les boutons sont affichés uniquement si tous les numéros de page numérique ne peut pas tenir

En outre, le GridView, DetailsView et FormView offrent la `PageIndex` et `PageCount` propriétés indiquant la page actuelle en cours de visualisation et le nombre total de pages de données, respectivement. Le `PageIndex` propriété est indexée, en commençant à 0, ce qui signifie que, lors de l’affichage de la première page de données `PageIndex` est égale à 0. `PageCount`, en revanche, démarre le comptage à 1, ce qui signifie que `PageIndex` est limité pour les valeurs comprises entre 0 et `PageCount - 1`.

Permettent de prendre un moment pour améliorer l’apparence par défaut de notre interface de pagination de GridView s s. Plus précisément, permettent de s que l’interface de pagination alignés à droite avec un fond gris clair. Au lieu de la définition de ces propriétés directement via le GridView s `PagerStyle` propriété s permettent de créer une classe CSS dans `Styles.css` nommé `PagerRowStyle` , puis affectez le `PagerStyle` s `CssClass` propriété via notre thème. Commencez par ouvrir `Styles.css` et de la définition de classe ajouter le code CSS suivant :


[!code-css[Main](paging-and-sorting-report-data-cs/samples/sample3.css)]

Ensuite, ouvrez le `GridView.skin` de fichiers dans le `DataWebControls` dossier dans le `App_Themes` dossier. Comme expliqué dans la *les Pages maîtres et Navigation du Site* les fichiers de didacticiel, apparence peuvent être utilisés pour spécifier les valeurs de propriété par défaut pour un contrôle Web. Par conséquent, augmenter les paramètres existants pour inclure le paramètre la `PagerStyle` s `CssClass` propriété `PagerRowStyle`. En outre, s permettent de configurer l’interface de pagination pour afficher au maximum cinq pages numériques boutons à l’aide de la `NumericFirstLast` interface de pagination.


[!code-aspx[Main](paging-and-sorting-report-data-cs/samples/sample4.aspx)]

## <a name="the-paging-user-experience"></a>L’expérience utilisateur de pagination

La figure 8 illustre la page web lorsque visité via un navigateur une fois que la case Activer la pagination de GridView s a été vérifiée et le `PagerStyle` et `PagerSettings` configurations ont été apportées via le `GridView.skin` fichier. Remarque uniquement dix enregistrements sont affichés et l’interface de pagination indique que nous visualisons la première page de données.


[![Avec la pagination activée, uniquement un sous-ensemble des enregistrements sont affichés à la fois](paging-and-sorting-report-data-cs/_static/image13.png)](paging-and-sorting-report-data-cs/_static/image12.png)

**Figure 8**: avec la pagination Enabled, uniquement un sous-ensemble des enregistrements sont affichés à la fois ([cliquez pour afficher l’image en taille réelle](paging-and-sorting-report-data-cs/_static/image14.png))


Lorsque l’utilisateur clique sur l’un des numéros de page dans l’interface de pagination, se poursuivent d’une publication (postback), la page recharge montrant que les enregistrements de la page s requis. La figure 9 illustre les résultats après vous être inscrit pour afficher la dernière page de données. Notez que la dernière page n’a seulement un seul enregistrement ; Il s’agit, car il existe des 81 enregistrements au total, qui résulte de huit pages de 10 enregistrements par page, plus une page avec un seul enregistrement.


[![En cliquant sur un numéro de Page provoque une publication (postback) et affiche le sous-ensemble approprié d’enregistrements](paging-and-sorting-report-data-cs/_static/image16.png)](paging-and-sorting-report-data-cs/_static/image15.png)

**Figure 9**: en cliquant sur un numéro de Page provoque une publication (postback) et affiche le sous-ensemble d’enregistrements appropriés ([cliquez pour afficher l’image en taille réelle](paging-and-sorting-report-data-cs/_static/image17.png))


## <a name="paging-s-server-side-workflow"></a>La pagination côté serveur s du flux de travail

Lorsque l’utilisateur final clique sur un bouton dans l’interface de pagination, une publication (postback) s’ensuit et commence par le workflow côté serveur suivant :

1. Le contrôle GridView s (ou DetailsView ou FormView) `PageIndexChanging` se déclenche des événements
2. ObjectDataSource demande nouveau *tous les* des données à partir de la couche BLL ; s GridView `PageIndex` et `PageSize` les valeurs de propriété sont utilisées pour déterminer les enregistrements retournés par la couche BLL à afficher dans le contrôle GridView
3. Les s GridView `PageIndexChanged` se déclenche des événements

À l’étape 2, ObjectDataSource demande nouveau toutes les données à partir de sa source de données. Ce style de pagination est communément appelé *pagination par défaut*, telle qu’elle s le comportement de pagination utilisé par défaut lors de la définition du `AllowPaging` propriété `true`. Valeur par défaut la pagination des données contrôle Web naïvement récupère tous les enregistrements pour chaque page de données, même si seulement un sous-ensemble des enregistrements réellement rendues dans le code HTML envoyé au navigateur s. À moins que la base de données est mis en cache par la couche BLL ou ObjectDataSource, pagination par défaut est rédhibitoire suffisamment grands jeux de résultats ou pour les applications web avec plusieurs utilisateurs simultanés.

Dans l’étape suivante du didacticiel, nous allons examiner comment implémenter *la pagination personnalisée*. Avec la pagination personnalisée, vous pouvez spécifiquement indiquer ObjectDataSource afin de récupérer l’ensemble précis des enregistrements nécessaires pour la page demandée de données. Comme vous pouvez l’imaginer, la pagination personnalisée améliore considérablement l’efficacité de la pagination des jeux de résultats volumineux.

> [!NOTE]
> Lors de la pagination par défaut ne c'est-à-dire pas lors de la pagination via suffisamment grands jeux de résultats ou pour les sites comportant de nombreux utilisateurs simultanés, gardez à l’esprit que la pagination personnalisée nécessite plus de modifications et d’effort pour implémenter et n’est pas aussi simple que la vérification de la case à cocher (comme c’est la valeur par défaut la pagination). Par conséquent, la pagination par défaut peut être le choix idéal pour les sites Web de petite taille, le trafic est faible ou lorsque la pagination des résultats relativement petit définit, telle qu’elle s beaucoup plus facile et plus rapide de les implémenter.


Par exemple, si nous savons que nous aurons jamais plus de 100 produits dans notre base de données, le gain de performances minime de la pagination personnalisée est probablement compensé par l’effort requis pour l’implémenter. Si, toutefois, nous pouvons un jour avoir des milliers ou des dizaines de milliers de produits, *pas* implémentant la pagination personnalisée serait entraver considérablement l’évolutivité de votre application.

## <a name="step-4-customizing-the-paging-experience"></a>Étape 4 : Personnalisation de l’expérience de la pagination

Les données des contrôles Web fournissent un nombre de propriétés qui peut être utilisé pour améliorer l’expérience de pagination utilisateur s. Le `PageCount` propriété, par exemple, indique que le nombre total de pages sont, tandis que le `PageIndex` propriété indique la page actuelle qui est visitée et peut être définie pour déplacer rapidement d’un utilisateur à une page spécifique. Pour illustrer l’utilisation de ces propriétés pour améliorer l’expérience de pagination utilisateur s, permettent d’ajouter une étiquette s Web le contrôle à notre page qui informe l’utilisateur quelle page ils re actuellement visite, ainsi que d’un contrôle DropDownList qui leur permet d’accéder rapidement à une page .

Tout d’abord, ajoutez un contrôle Web Label à votre page, définissez son `ID` propriété `PagingInformation`et d’effacer les son `Text` propriété. Ensuite, créez un gestionnaire d’événements pour le contrôle GridView s `DataBound` événement et ajoutez le code suivant :


[!code-csharp[Main](paging-and-sorting-report-data-cs/samples/sample5.cs)]

Ce gestionnaire d’événements affecte le `PagingInformation` étiquette s `Text` propriété à un message informant l’utilisateur de la page qu’ils visitent `Products.PageIndex + 1` hors le nombre total de pages `Products.PageCount` (nous ajouter 1 à la `Products.PageIndex` propriété car `PageIndex` est indexée à partir de 0). Vous avez choisi l’attribuer cette étiquette s `Text` propriété dans le `DataBound` Gestionnaire d’événements par opposition à la `PageIndexChanged` Gestionnaire d’événements, car le `DataBound` événement est déclenché chaque fois qu’elles sont liées au GridView alors que le `PageIndexChanged` Gestionnaire d’événements se déclenche lorsque l’index de page est modifiée. Lorsque le contrôle GridView est initialement liée aux données sur la première page visiter le `PageIndexChanging` événement déclencheur de t ne (alors que le `DataBound` événement).

Avec cet ajout, l’utilisateur est à présent affiché un message indiquant quelle page ils visitent et le nombre total de pages de données est.


[![Le numéro de Page actuel et le nombre Total de Pages sont affichés.](paging-and-sorting-report-data-cs/_static/image19.png)](paging-and-sorting-report-data-cs/_static/image18.png)

**La figure 10**: le numéro de Page actuel et le nombre Total de Pages sont affichés ([cliquez pour afficher l’image en taille réelle](paging-and-sorting-report-data-cs/_static/image20.png))


Outre le contrôle d’étiquette, permettent de s également ajouter un contrôle DropDownList qui répertorie les numéros de page dans le GridView avec la page affichée actuellement sélectionnée. L’idée est que l’utilisateur peut accéder rapidement à partir de la page actuelle vers une autre en sélectionnant simplement le nouvel index de page dans la liste déroulante. Commencez par ajouter une liste déroulante vers le concepteur, en définissant ses `ID` propriété `PageList` et la vérification de l’option Activer AutoPostBack à partir de sa balise active.

Revenez ensuite à la `DataBound` Gestionnaire d’événements et ajoutez le code suivant :


[!code-csharp[Main](paging-and-sorting-report-data-cs/samples/sample6.cs)]

Ce code commence en supprimant les éléments dans le `PageList` DropDownList. Cela peut sembler superflu, dans la mesure où un commode t pensez que le nombre de pages à modifier, mais les autres utilisateurs peuvent l’utilisation du système simultanément, ajouter ou supprimer des enregistrements à partir de la `Products` table. Le nombre de pages de données peuvent altérer les insertions ou les suppressions.

Ensuite, nous avons besoin recréer les numéros de page et celui qui est mappé au GridView actuel `PageIndex` sélectionné par défaut. Effectuer cette opération avec une boucle de 0 à `PageCount - 1`, ajout d’un nouveau `ListItem` dans chaque itération et en attribuant son `Selected` propriété la valeur true si l’index d’itération actuelle est égale à la s GridView `PageIndex` propriété.

Enfin, nous devons créer un gestionnaire d’événements pour les opérations de mappage DropDownList `SelectedIndexChanged` événement qui se déclenche chaque fois que l’utilisateur choisir un autre élément dans la liste. Pour créer ce gestionnaire d’événements, double-cliquez sur DropDownList dans le concepteur, puis ajoutez le code suivant :


[!code-csharp[Main](paging-and-sorting-report-data-cs/samples/sample7.cs)]

Comme le montre la Figure 11, modifiez le s GridView `PageIndex` propriété entraîne les données à être reliée au GridView. Dans le GridView s `DataBound` Gestionnaire d’événements, DropDownList approprié `ListItem` est sélectionnée.


[![L’utilisateur est automatiquement dirigé vers la sixième Page lors de la sélection de l’élément de liste déroulante de Page 6](paging-and-sorting-report-data-cs/_static/image22.png)](paging-and-sorting-report-data-cs/_static/image21.png)

**Figure 11**: l’utilisateur est automatiquement dirigé vers la sixième Page lors de la sélection de l’élément de liste déroulante de Page 6 ([cliquez pour afficher l’image en taille réelle](paging-and-sorting-report-data-cs/_static/image23.png))


## <a name="step-5-adding-bi-directional-sorting-support"></a>Étape 5 : Ajout de prise en charge du tri bidirectionnelle

Ajout de prise en charge du tri bidirectionnelle est aussi simple que l’ajout de prise en charge la pagination Cochez simplement l’option Activer le tri de la balise active de s GridView (qui définit les opérations de mappage GridView [ `AllowSorting` propriété](https://msdn.microsoft.com/en-US/library/system.web.ui.webcontrols.gridview.allowsorting.aspx) à `true`). Cela restitue chacune des en-têtes des champs s GridView comme LinkButton qui, lorsque vous cliquez sur, provoquent une publication (postback) et retourner les données triées par la colonne sélectionnée dans l’ordre croissant. Cliquez de nouveau sur le même en-tête LinkButton nouveau tri basé sur les données dans l’ordre décroissant.

> [!NOTE]
> Si vous utilisez une couche d’accès aux données personnalisées plutôt que d’un DataSet typé, peut-être pas une option d’activer le tri dans la balise active de GridView s. Uniquement les contrôles GridView liés à des sources de données qui prennent en charge le tri a cette case à cocher est disponible. Le DataSet typé fournit la prise en charge du tri d’out of box, car la table de données ADO.NET fournit un `Sort` méthode qui, lorsqu’elle est appelée, trie les s DataTable DataRows à l’aide des critères spécifiés.


Si votre couche DAL ne retourne pas les objets en mode natif la prise en charge le tri que vous devez configurer l’ObjectDataSource pour transmettre des informations de tri à la couche de logique métier, qui peuvent trier les données ou les données trié par la couche DAL. Nous allons observer comment trier des données au niveau de la logique métier et les couches d’accès aux données dans un didacticiel futures.

Le tri LinkButton est rendus sous forme de liens hypertexte HTML, dont les couleurs actuels (bleus pour une liaison non visitée et rouge foncé pour un lien visité) sont en conflit avec la couleur d’arrière-plan de la ligne d’en-tête. Au lieu de cela, permettent de s ont tous les liens de ligne d’en-tête affichées en blanc, indépendamment de si elles ve été visité ou non. Cela peut être accompli en ajoutant le code suivant à la `Styles.css` classe :


[!code-css[Main](paging-and-sorting-report-data-cs/samples/sample8.css)]

Cette syntaxe indique l’utilisation d’un texte blanc lors de l’affichage de ces liens hypertexte dans un élément qui utilise la classe HeaderStyle.

Après cet ajout CSS, lors de la visite de la page via un navigateur votre écran doit ressembler à la Figure 12. En particulier, la Figure 12 montre les résultats après avoir cliqué sur le lien d’en-tête prix champ s.


[![Les résultats ont été triés par le prix unitaire dans l’ordre croissant](paging-and-sorting-report-data-cs/_static/image25.png)](paging-and-sorting-report-data-cs/_static/image24.png)

**Figure 12**: les résultats ont été triés par le prix unitaire par ordre croissant ([cliquez pour afficher l’image en taille réelle](paging-and-sorting-report-data-cs/_static/image26.png))


## <a name="examining-the-sorting-workflow"></a>Examen du flux de travail de tri

GridView tous les champs du BoundField, CheckBoxField, TemplateField, et ainsi de suite ont un `SortExpression` propriété qui indique l’expression qui doit être utilisée pour trier les données de cas de clic sur s champ lien de l’en-tête de tri. Le contrôle GridView a également un `SortExpression` propriété. Lorsque LinkButton clic est effectué sur un en-tête de tri, le contrôle GridView affecte ce champ s `SortExpression` valeur ses `SortExpression` propriété. Ensuite, les données sont ré-extraite ObjectDataSource et triées en fonction de la s GridView `SortExpression` propriété. La liste suivante décrit la séquence d’étapes qui apparaît lorsqu’un utilisateur final trie les données dans un GridView :

1. Le contrôle GridView s [événement Sorting](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.sorting(VS.80).aspx) se déclenche
2. Le GridView s [ `SortExpression` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.sortexpression.aspx) est définie sur la `SortExpression` du champ dont l’en-tête tri LinkButton l’utilisateur a cliqué
3. ObjectDataSource nouveau récupère toutes les données à partir de la couche BLL et trie ensuite les données à l’aide de la s GridView`SortExpression`
4. Les s GridView `PageIndex` propriété est réinitialisée à 0, ce qui signifie que, lors du tri de l’utilisateur est renvoyé à la première page de données (en supposant la prise en charge la pagination a été implémenté)
5. Le contrôle GridView s [ `Sorted` événement](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.sorted(VS.80).aspx) se déclenche

Comme avec la pagination de la valeur par défaut, la valeur par défaut, option de tri récupère nouveau *tous les* des enregistrements à partir de la couche BLL. Lors de l’utilisation de tri sans pagination ou lors de l’utilisation de tri avec valeur par défaut la pagination, là s aucun moyen de contourner ces performances atteint (au niveau de la mise en cache de la base de données). Toutefois, comme nous le verrons dans un futur didacticiel, il s possible de trier les données de manière efficace lors de l’utilisation de la pagination personnalisée.

Lorsque vous liez un ObjectDataSource au contrôle GridView dans la liste déroulante dans la balise active de GridView s, chaque champ GridView a automatiquement son `SortExpression` propriété affectée au nom du champ de données dans la `ProductsRow` classe. Par exemple, le `ProductName` BoundField s `SortExpression` a la valeur `ProductName`, comme indiqué dans le balisage déclaratif suivant :


[!code-aspx[Main](paging-and-sorting-report-data-cs/samples/sample9.aspx)]

Un champ peut être configuré afin qu’il s ne pouvant pas être trié en supprimant son `SortExpression` propriété (l’affectation d’une chaîne vide). Pour illustrer cela, supposons que nous ne t souhaitez permettent de trier les prix de nos produits nos clients. Le `UnitPrice` BoundField s `SortExpression` propriété peut être supprimée à partir du balisage déclaratif ou via la boîte de dialogue champs (qui est accessible en cliquant sur le lien Modifier les colonnes dans la balise active de GridView s).


![Les résultats ont été triés par le prix unitaire dans l’ordre croissant](paging-and-sorting-report-data-cs/_static/image27.png)

**Figure 13**: les résultats ont été triés par le prix unitaire dans l’ordre croissant


Une fois la `SortExpression` propriété a été supprimée pour le `UnitPrice` BoundField, l’en-tête est rendu sous forme de texte plutôt que sous forme de lien, empêchant ainsi les utilisateurs de trier les données en cours.


[![En supprimant la propriété SortExpression, les utilisateurs peuvent trier ne sont plus les produits par prix](paging-and-sorting-report-data-cs/_static/image29.png)](paging-and-sorting-report-data-cs/_static/image28.png)

**La figure 14**: en supprimant la propriété SortExpression, les utilisateurs peuvent trier n’est plus le prix de produits ([cliquez pour afficher l’image en taille réelle](paging-and-sorting-report-data-cs/_static/image30.png))


## <a name="programmatically-sorting-the-gridview"></a>De tri par programmation le contrôle GridView

Vous pouvez également trier par programme le contenu du contrôle GridView à l’aide du contrôle GridView s [ `Sort` méthode](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.sort.aspx). Il suffit de passer le `SortExpression` valeur pour les trier avec la [ `SortDirection` ](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.sortdirection.aspx) (`Ascending` ou `Descending`), et les données de s GridView seront retriées.

Imaginez que la raison pour laquelle nous avons désactivé en triant le `UnitPrice` était car nous inquiétiez que nos clients achèterait simplement les produits moins cher. Toutefois, nous souhaitons les encourager à acheter les produits les plus chers, afin que nous d comme qu’ils soient en mesure de trier les produits par prix, mais uniquement à partir du prix le plus cher au moins.

Pour accomplir cela ajouter un contrôle bouton Web à la page, définissez son `ID` propriété `SortPriceDescending`et son `Text` propriété trier par prix. Ensuite, créez un gestionnaire d’événements pour le bouton s `Click` événement en double-cliquant sur le contrôle de bouton dans le concepteur. Pour ce gestionnaire d’événements, ajoutez le code suivant :


[!code-csharp[Main](paging-and-sorting-report-data-cs/samples/sample10.cs)]

Ce bouton renvoie l’utilisateur à la première page avec les produits triés par prix, du plus cher au moins cher (voir Figure 15).


[![Cliquez sur le bouton trie les produits à partir de la plus coûteuse au moins](paging-and-sorting-report-data-cs/_static/image32.png)](paging-and-sorting-report-data-cs/_static/image31.png)

**Figure 15**: en cliquant sur le bouton trie les produits à partir de la plus coûteuse au moins ([cliquez pour afficher l’image en taille réelle](paging-and-sorting-report-data-cs/_static/image33.png))


## <a name="summary"></a>Résumé

Dans ce didacticiel, que nous avons vu comment implémenter la pagination et des fonctionnalités de tri par défaut, qui a été aussi simples que la vérification de la case à cocher. Lorsqu’un utilisateur trie ou des pages de données, un flux de travail similaire se déroule :

1. Une publication (postback) s’ensuit
2. Les données de contrôle de Web s niveau préalable se déclenche des événements (`PageIndexChanging` ou `Sorting`)
3. Toutes les données sont récupérées de nouveau par ObjectDataSource
4. Les données de contrôle de Web s postérieures se déclenche des événements de niveau (`PageIndexChanged` ou `Sorted`)

Lors de l’implémentation de la pagination de base et le tri est un jeu d’enfant, davantage d’efforts doit être exercée d’utiliser la pagination personnalisée plus efficace ou pour améliorer l’interface de pagination ou de tri. Didacticiels futures explorerons ces rubriques.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Next](efficiently-paging-through-large-amounts-of-data-cs.md)
