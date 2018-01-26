---
uid: web-forms/overview/data-access/basic-reporting/displaying-data-with-the-objectdatasource-cs
title: "Affichage des données avec ObjectDataSource (c#) | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel présente le contrôle ObjectDataSource à l’aide de ce contrôle, que vous pouvez lier des données récupérées à partir de la couche BLL créée dans le didacticiel précédent sans havi..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2010
ms.topic: article
ms.assetid: af882aef-56f5-4e9a-8f95-3977fde20e74
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/basic-reporting/displaying-data-with-the-objectdatasource-cs
msc.type: authoredcontent
ms.openlocfilehash: 8bd6534b652735e657aa71cdf07dac48f20a549c
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="displaying-data-with-the-objectdatasource-c"></a>Affichage des données avec ObjectDataSource (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/6/3/463cf87c-4724-4cbc-b7b5-3f866f43ba50/ASPNET_Data_Tutorial_4_CS.exe) ou [télécharger le PDF](displaying-data-with-the-objectdatasource-cs/_static/datatutorial04cs1.pdf)

> Ce didacticiel présente le contrôle ObjectDataSource à l’aide de ce contrôle, que vous pouvez lier des données récupérées à partir de la couche BLL créée dans le didacticiel précédent, sans avoir à écrire une ligne de code !


## <a name="introduction"></a>Introduction

Notre application architecture et le site Web de mise en page terminée, nous sommes prêts à commencer à Explorer les instructions permettant d’effectuer diverses tâches courantes de données et création de rapports liés. Dans les didacticiels précédents, nous avons vu comment lier par programmation les données à partir de la couche DAL et la couche BLL à un contrôle Web dans une page ASP.NET de données. Cette syntaxe attribuant les données contrôle Web `DataSource` propriété aux données à afficher et l’appel du contrôle `DataBind()` méthode était le modèle utilisé dans les applications ASP.NET 1.x et pouvez continuer à utiliser dans vos 2.0 applications. Toutefois, les nouveaux contrôles de source de données d’ASP.NET 2.0 offrent une façon déclarative des données. À l’aide de ces contrôles, vous pouvez lier des données récupérées à partir de la couche BLL créé dans le [didacticiel précédent](../introduction/creating-a-business-logic-layer-cs.md) sans avoir à écrire une ligne de code !

ASP.NET 2.0 est livré avec cinq contrôles de source de données intégrés [SqlDataSource](https://msdn.microsoft.com/library/dz12d98w(vs.80).aspx), [AccessDataSource](https://msdn.microsoft.com/library/8e5545e1.aspx), [ObjectDataSource](https://msdn.microsoft.com/library/9a4kyhcx.aspx), [XmlDataSource](https://msdn.microsoft.com/library/e8d8587a(en-US,VS.80).aspx), et [SiteMapDataSource](https://msdn.microsoft.com/library/5ex9t96x(en-US,VS.80).aspx) bien que vous pouvez créer votre propre [contrôles de source de données personnalisé](https://msdn.microsoft.com/library/default.asp?url=/library/dnvs05/html/DataSourceCon1.asp), si nécessaire. Étant donné que nous avons développé une architecture pour notre application du didacticiel, nous utiliserons ObjectDataSource par rapport aux classes de notre BLL.


![ASP.NET 2.0 comprend cinq contrôles de Source de données intégrés](displaying-data-with-the-objectdatasource-cs/_static/image1.png)

**Figure 1**: ASP.NET 2.0 inclut les cinq contrôles de Source de données intégrés


ObjectDataSource sert de proxy pour travailler avec un autre objet. Pour configurer l’ObjectDataSource nous spécifions cela sous-jacent objet ainsi que ses méthodes de mappent pour l’ObjectDataSource `Select`, `Insert`, `Update`, et `Delete` méthodes. Une fois que cet objet sous-jacent a été spécifié et ses méthodes mappé à l’ObjectDataSource, vous pouvez ensuite lier ObjectDataSource à un contrôle Web de données. ASP.NET est fourni avec les données de nombreux contrôles Web, y compris le GridView, DetailsView, RadioButtonList et DropDownList, entre autres. Pendant le cycle de vie de page, les données de contrôle Web devront peut-être accéder aux données qu’il est lié, il se feront en appelant son ObjectDataSource `Select` méthode ; si les données de contrôle Web prend en charge l’insertion, de mise à jour, ou de suppression, les appels peuvent être effectués à son ObjectDataSource `Insert`, `Update`, ou `Delete` méthodes. Ces appels sont ensuite routés par ObjectDataSource aux méthodes de l’objet sous-jacent approprié, comme l’illustre le diagramme suivant.


[![ObjectDataSource sert de Proxy](displaying-data-with-the-objectdatasource-cs/_static/image3.png)](displaying-data-with-the-objectdatasource-cs/_static/image2.png)

**Figure 2**: le ObjectDataSource sert de Proxy ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image4.png))


Si l’ObjectDataSource peut être utilisé pour appeler des méthodes d’insertion, mise à jour ou supprimer des données, nous allons vous concentrer uniquement sur Renvoi de données ; didacticiels futures allez Explorer à l’aide de la ObjectDataSource et les données des contrôles Web qui modifient les données.

## <a name="step-1-adding-and-configuring-the-objectdatasource-control"></a>Étape 1 : Ajout et configuration du contrôle ObjectDataSource

Commencez par ouvrir le `SimpleDisplay.aspx` page dans le `BasicReporting` dossier, basculez en mode Design, puis faites glisser un contrôle ObjectDataSource à partir de la boîte à outils vers l’aire de conception de la page. ObjectDataSource apparaît sous la forme d’une zone grise sur l’aire de conception, car elle ne produit pas tout balisage ; elle accède simplement à des données en appelant une méthode à partir d’un objet spécifié. Les données retournées par un ObjectDataSource peuvent être affichées par un contrôle Web, tels que le GridView, DetailsView, FormView et ainsi de suite de données.

> [!NOTE]
> Ou bien, vous devrez peut-être ajouter les données de contrôle Web à la page et, à partir de sa balise active, puis le &lt;nouvelle source de données&gt; option dans la liste déroulante.


Pour spécifier l’objet sous-jacent de l’ObjectDataSource et comment mappent les méthodes de l’objet à l’ObjectDataSource, cliquez sur le lien configurer la Source de données à partir de la balise de l’ObjectDataSource.


[![Cliquez sur le lien de Source de données à partir de la balise active de configuration](displaying-data-with-the-objectdatasource-cs/_static/image6.png)](displaying-data-with-the-objectdatasource-cs/_static/image5.png)

**Figure 3**: cliquez sur le lien de Source de données de configuration à partir de la balise active ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image7.png))


L’Assistant Configurer la Source de données s’affiche. Tout d’abord, nous devons spécifier l’objet Qu'objectdatasource consiste à utiliser. Si la case à cocher « Afficher uniquement les composants de données » est cochée, la liste déroulante dans cet écran répertorie uniquement les objets qui ont été décorées avec le `DataObject` attribut. Actuellement, notre liste inclut les TableAdapters dans le DataSet typé et les classes de la couche BLL que nous avons créé dans le didacticiel précédent. Si vous avez oublié d’ajouter le `DataObject` attribut pour les classes de la couche de logique métier vous ne les voyez dans cette liste. Dans ce cas, désactivez la case à cocher « Afficher uniquement les composants de données » pour afficher tous les objets, qui doivent inclure les classes de la couche BLL (ainsi que d’autres classes dans le DataSet typé DataTables, DataRows et ainsi de suite).

Dans le premier écran, choisissez la `ProductsBLL` classe dans la liste déroulante et cliquez sur Suivant.


[![Spécifiez l’objet à utiliser avec le contrôle ObjectDataSource](displaying-data-with-the-objectdatasource-cs/_static/image9.png)](displaying-data-with-the-objectdatasource-cs/_static/image8.png)

**Figure 4**: spécifiez l’objet à utiliser avec le contrôle ObjectDataSource ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image10.png))


L’écran suivant de l’Assistant vous invite à sélectionner quelle méthode ObjectDataSource doit appeler. La liste déroulante répertorie les méthodes qui retournent des données dans l’objet sélectionné à partir de l’écran précédent. Nous voyons ici `GetProductByProductID`, `GetProducts`, `GetProductsByCategoryID`, et `GetProductsBySupplierID`. Sélectionnez le `GetProducts` méthode dans la liste déroulante, cliquez sur Terminer (si vous avez ajouté le `DataObjectMethodAttribute` à la `ProductBLL`de méthodes, comme indiqué dans le didacticiel précédent, cette option seront sélectionnés par défaut).


[![Choisissez la méthode pour retourner des données à partir de l’onglet Sélection](displaying-data-with-the-objectdatasource-cs/_static/image12.png)](displaying-data-with-the-objectdatasource-cs/_static/image11.png)

**Figure 5**: choisissez la méthode pour retourner des données à partir de l’onglet Sélectionner ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image13.png))


## <a name="configure-the-objectdatasource-manually"></a>Configurer manuellement les ObjectDataSource

Assistant Configurer la Source de données de l’ObjectDataSource offre un moyen rapide pour spécifier l’objet qu’il utilise et pour associer les méthodes de l’objet sont appelées. Vous pouvez, toutefois, configurer ObjectDataSource via ses propriétés, via la fenêtre Propriétés ou directement dans le balisage déclaratif. Il suffit de définir la `TypeName` propriété au type de l’objet sous-jacent à utiliser et le `SelectMethod` à la méthode à appeler lors de la récupération des données.


[!code-aspx[Main](displaying-data-with-the-objectdatasource-cs/samples/sample1.aspx)]

Même si vous préférez l’Assistant Configurer la Source de données il peut arriver lorsque vous devez configurer manuellement l’ObjectDataSource, comme l’Assistant répertorie uniquement les classes créées par le développeur. Si vous souhaitez lier ObjectDataSource à une classe dans le .NET Framework, tels que les [classe d’appartenance](https://msdn.microsoft.com/library/system.web.security.membership.aspx), pour accéder aux informations de compte d’utilisateur, ou le [classe active](https://msdn.microsoft.com/library/system.io.directory.aspx) pour travailler avec les informations de système de fichiers Vous devez définir manuellement les propriétés de l’ObjectDataSource.

## <a name="step-2-adding-a-data-web-control-and-binding-it-to-the-objectdatasource"></a>Étape 2 : Ajout d’un contrôle Web de données et en la liant à ObjectDataSource

Une fois l’ObjectDataSource a été ajouté à la page et configuré, nous sommes prêts à ajouter des contrôles Web de données à la page pour afficher les données retournées par l’ObjectDataSource `Select` (méthode). Toutes les données de contrôle Web peuvent être liées à un ObjectDataSource ; Examinons à présent afficher les données de l’ObjectDataSource dans un GridView, DetailsView et FormView.

## <a name="binding-a-gridview-to-the-objectdatasource"></a>Liaison d’un contrôle GridView à ObjectDataSource

Ajouter un contrôle GridView à partir de la boîte à outils `SimpleDisplay.aspx`d’aire de conception. À partir de la balise de GridView, choisissez le contrôle ObjectDataSource que nous avons ajouté à l’étape 1. Cette opération crée automatiquement un BoundField dans le GridView pour chaque propriété retournée par les données à partir de l’ObjectDataSource `Select` (méthode) (à savoir, les propriétés définies par le DataTable de produits).


[![Un GridView a été ajouté à la Page et lié à ObjectDataSource](displaying-data-with-the-objectdatasource-cs/_static/image15.png)](displaying-data-with-the-objectdatasource-cs/_static/image14.png)

**Figure 6**: un GridView a été ajouté à la Page et la limite de ObjectDataSource ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image16.png))


Vous pouvez ensuite personnaliser, réorganiser ou supprimer BoundFields du GridView en cliquant sur l’option Modifier les colonnes à partir de la balise active.


[![Gérer les BoundFields du GridView via la boîte de dialogue Modifier les colonnes](displaying-data-with-the-objectdatasource-cs/_static/image18.png)](displaying-data-with-the-objectdatasource-cs/_static/image17.png)

**Figure 7**: gérer BoundFields via les colonnes boîte du GridView de dialogue Modifier ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image19.png))


Prenez un moment pour modifier BoundFields du GridView, suppression de la `ProductID`, `SupplierID`, `CategoryID`, `QuantityPerUnit`, `UnitsInStock`, `UnitsOnOrder`, et `ReorderLevel` BoundFields. Sélectionnez le BoundField à partir de la liste dans la coin inférieur gauche et cliquez sur le bouton Supprimer (le X rouge) pour les supprimer. Ensuite, réorganiser le BoundFields afin que le `CategoryName` et `SupplierName` BoundFields précéder le `UnitPrice` BoundField en sélectionnant ces BoundFields en cliquant sur la flèche vers le haut. Définir le `HeaderText` propriétés de la BoundFields restants à `Products`, `Category`, `Supplier`, et `Price`, respectivement. Ensuite, demandez à le `Price` BoundField mis en forme comme une valeur monétaire en définissant le BoundField `HtmlEncode` propriété sur False et son `DataFormatString` propriété `{0:c}`. Enfin, aligner horizontalement la `Price` à droite et la `Discontinued` case à cocher dans le centre de via le `ItemStyle` / `HorizontalAlign` propriété.


[!code-aspx[Main](displaying-data-with-the-objectdatasource-cs/samples/sample2.aspx)]


[![Le contrôle du GridView BoundFields ont été personnalisées.](displaying-data-with-the-objectdatasource-cs/_static/image21.png)](displaying-data-with-the-objectdatasource-cs/_static/image20.png)

**Figure 8**: BoundFields ont été personnalisées du GridView ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image22.png))


## <a name="using-themes-for-a-consistent-look"></a>Utilisation de thèmes pour une apparence cohérente

Ces didacticiels s’efforcent supprimer tous les paramètres de style de niveau de contrôle, au lieu d’utiliser les feuilles de style en cascade définies dans un fichier externe, chaque fois que possible. Le `Styles.css` fichier contient `DataWebControlStyle`, `HeaderStyle`, `RowStyle`, et `AlternatingRowStyle` des classes CSS qui doivent être utilisés pour dicter l’apparence des données Web contrôles utilisés dans ces didacticiels. Pour ce faire, nous avons défini de GridView `CssClass` propriété `DataWebControlStyle`et son `HeaderStyle`, `RowStyle`, et `AlternatingRowStyle` propriétés `CssClass` propriétés en conséquence.

Si vous définissez ces `CssClass` propriétés au contrôle Web n’oubliez pas de définir explicitement ces valeurs de propriété pour les données de chaque devons-nous Web contrôle ajouté à nos didacticiels. Une approche plus gérable consiste à définir les propriétés CSS par défaut pour le contrôle GridView, DetailsView, et FormView contrôle à l’aide d’un thème. Un thème est une collection de paramètres de propriété de niveau de contrôle, des images et des classes CSS qui peuvent être appliquées aux pages sur un site pour appliquer une apparence commune.

Notre thème n’inclut pas toutes les images ou des fichiers CSS (nous laisserons à la feuille de style `Styles.css` comme-est, défini dans le dossier racine de l’application web), mais n’inclut pas les deux enveloppes. Une apparence est un fichier qui définit les propriétés par défaut pour un contrôle Web. En particulier, nous allons avoir un fichier d’apparence pour les contrôles GridView et DetailsView, indiquant la valeur par défaut `CssClass`-propriétés associées.

Commencez par ajouter un nouveau fichier d’apparence à votre projet appelé `GridView.skin` en cliquant sur le nom du projet dans l’Explorateur de solutions et en choisissant Ajouter un nouvel élément.


[![Ajouter un fichier d’apparence nommé GridView.skin](displaying-data-with-the-objectdatasource-cs/_static/image24.png)](displaying-data-with-the-objectdatasource-cs/_static/image23.png)

**Figure 9**: ajouter un fichier d’apparence nommé `GridView.skin` ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image25.png))


Fichiers d’apparence doivent être placés dans un thème, qui se trouvent dans le `App_Themes` dossier. Étant donné que nous n’avons pas encore un tel dossier, Visual Studio proposent bien à en créer un pour nous lors de l’ajout de l’apparence de notre premier. Cliquez sur Oui pour créer le `App_Theme` dossier et placer le nouveau `GridView.skin` fichier il.


[![Visual Studio permettent de créer le dossier App_Theme](displaying-data-with-the-objectdatasource-cs/_static/image27.png)](displaying-data-with-the-objectdatasource-cs/_static/image26.png)

**La figure 10**: permettent de Visual Studio crée le `App_Theme` dossier ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image28.png))


Cette opération crée un nouveau thème dans le `App_Themes` dossier nommé GridView avec le fichier d’apparence `GridView.skin`.


![Le thème du GridView a été ajouté au dossier App_Theme](displaying-data-with-the-objectdatasource-cs/_static/image29.png)

**Figure 11**: le thème du GridView a été ajouté à la `App_Theme` dossier


Remplacez le nom du GridView thème DataWebControls (avec le bouton droit sur le dossier GridView dans le `App_Theme` dossier et choisissez Renommer). Ensuite, entrez le balisage suivant dans la `GridView.skin` fichier :


[!code-aspx[Main](displaying-data-with-the-objectdatasource-cs/samples/sample3.aspx)]

Définit les propriétés par défaut pour le `CssClass`-en fonction des propriétés de n’importe quel GridView dans n’importe quelle page qui utilise le DataWebControls thème. Vous allez ajouter une autre apparence pour le contrôle DetailsView, un contrôle Web qui nous utiliserons peu de données. Ajouter une nouvelle apparence pour le thème DataWebControls nommé `DetailsView.skin` et ajoutez le balisage suivant :


[!code-aspx[Main](displaying-data-with-the-objectdatasource-cs/samples/sample4.aspx)]

Avec nos thème défini, la dernière étape consiste à appliquer le thème à la page ASP.NET. Un thème peut être appliqué sur une base de page par page ou pour toutes les pages dans un site Web. Nous allons utiliser ce thème pour toutes les pages dans le site Web. Pour ce faire, ajoutez le balisage suivant à `Web.config`de `<system.web>` section :


[!code-xml[Main](displaying-data-with-the-objectdatasource-cs/samples/sample5.xml)]

C’est tout cela ! Le `styleSheetTheme` paramètre indique que les propriétés spécifiées dans le thème doivent *pas* remplacer les propriétés spécifiées au niveau du contrôle. Pour spécifier que les paramètres de thème doivent choisir un autre contrôle les paramètres, utilisez le `theme` d’attribut à la place de `styleSheetTheme`; Malheureusement, les paramètres de thème spécifiés via la `theme` attribut n’apparaissent pas dans la vue de conception de Visual Studio. Reportez-vous à [thèmes ASP.NET et vue d’ensemble des apparences](https://msdn.microsoft.com/library/ykzx33wh.aspx) et [côté serveur Styles à l’aide de thèmes](https://quickstarts.asp.net/quickstartv20/aspnet/doc/themes/stylesheettheme.aspx) pour plus d’informations sur les thèmes et les apparences ; consultez [Comment : appliquer des thèmes ASP.NET](https://msdn.microsoft.com/library/0yy5hxdk(VS.80).aspx) pour plus d’informations sur configuration d’une page pour utiliser un thème.


[![Le contrôle GridView affiche le nom du produit, catégorie, fournisseur, prix et informations supprimées](displaying-data-with-the-objectdatasource-cs/_static/image31.png)](displaying-data-with-the-objectdatasource-cs/_static/image30.png)

**Figure 12**: le contrôle GridView affiche le nom du produit, catégorie, fournisseur, prix et supprimées des informations ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image32.png))


## <a name="displaying-one-record-at-a-time-in-the-detailsview"></a>Affichage d’un enregistrement à la fois dans le contrôle DetailsView

Le contrôle GridView affiche une ligne pour chaque enregistrement retourné par le contrôle de source de données à laquelle elle est liée. Il n’y a envie, toutefois, nous pouvons être amenés à afficher un enregistrement unique ou qu’un seul enregistrement à la fois. Le [contrôle DetailsView](https://msdn.microsoft.com/library/s3w1w7t4.aspx) offre cette fonctionnalité, rendu sous la forme d’un élément HTML `<table>` avec deux colonnes et une ligne pour chaque colonne ou propriété liée au contrôle. Vous pouvez considérer le contrôle DetailsView comme un GridView avec un seul enregistrement avec une rotation de 90 degrés.

Commencez par ajouter un contrôle DetailsView *ci-dessus* le contrôle GridView dans `SimpleDisplay.aspx`. Ensuite, le lier au contrôle ObjectDataSource même comme le GridView. Comme avec le GridView, un BoundField sera ajouté au contrôle DetailsView pour chaque propriété de l’objet retourné par l’ObjectDataSource `Select` (méthode). La seule différence est que le contrôle du DetailsView BoundFields sont disposés horizontalement plutôt que verticalement.


[![Ajouter un contrôle DetailsView à la Page et la lier à ObjectDataSource](displaying-data-with-the-objectdatasource-cs/_static/image34.png)](displaying-data-with-the-objectdatasource-cs/_static/image33.png)

**Figure 13**: ajouter un contrôle DetailsView à la Page et la lier à ObjectDataSource ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image35.png))


Comme le GridView, BoundFields du DetailsView peuvent être modifiés pour fournir un affichage plus personnalisé des données retournées par ObjectDataSource. La figure 14 illustre le contrôle DetailsView après son BoundFields et `CssClass` propriétés ont été configurées pour rendre son apparence semblable à l’exemple de GridView.


[![Le contrôle DetailsView affiche un enregistrement unique](displaying-data-with-the-objectdatasource-cs/_static/image37.png)](displaying-data-with-the-objectdatasource-cs/_static/image36.png)

**La figure 14**: le contrôle DetailsView affiche un enregistrement unique ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image38.png))


Notez que le contrôle DetailsView affiche uniquement le premier enregistrement retourné par la source de données. Pour autoriser l’utilisateur à parcourir tous les enregistrements à la fois, nous devons activer la pagination pour le contrôle DetailsView. Pour ce faire, revenez à Visual Studio et vérifiez la case à cocher Activer la pagination dans la balise active du DetailsView.


[![Activer la pagination dans le contrôle DetailsView](displaying-data-with-the-objectdatasource-cs/_static/image40.png)](displaying-data-with-the-objectdatasource-cs/_static/image39.png)

**Figure 15**: activer la pagination dans le contrôle DetailsView ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image41.png))


[![Avec la pagination est activée, le contrôle DetailsView permet à l’utilisateur afficher les produits](displaying-data-with-the-objectdatasource-cs/_static/image43.png)](displaying-data-with-the-objectdatasource-cs/_static/image42.png)

**Figure 16**: avec la pagination activé, le contrôle DetailsView permet à l’utilisateur afficher les produits ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image44.png))


Nous y reviendrons plus tard la pagination dans les futures didacticiels.

## <a name="a-more-flexible-layout-for-showing-one-record-at-a-time"></a>Une mise en page plus souple pour afficher un enregistrement à la fois

Le contrôle DetailsView est assez rigid dans comment il s’affiche chaque enregistrement retourné à partir de ObjectDataSource. Nous pouvons adopter une vue plus souple des données. Par exemple, au lieu d’afficher le nom du produit, catégorie, fournisseur, prix et informations supprimées chaque sur une ligne distincte, nous pouvons adopter afficher le nom du produit et de prix dans une `<h4>` titre, avec les informations de catégorie et de fournisseur figurant sous le nom et le prix de la taille de police. Et nous pouvons se soucient pas à afficher les noms de propriété (produit, catégorie et ainsi de suite) en regard de valeurs.

Le [contrôle FormView](https://msdn.microsoft.com/library/fyf1dk77.aspx) fournit ce niveau de personnalisation. Au lieu d’utiliser des champs (comme le GridView et DetailsView), le FormView utilise des modèles, qui autorisent une combinaison de contrôles Web, HTML statique, et [syntaxe de liaison de données](http://www.15seconds.com/issue/040630.htm). Si vous êtes familiarisé avec le contrôle du répéteur à partir de ASP.NET 1.x, vous pouvez considérer le FormView comme répéteur pour afficher un seul enregistrement.

Ajouter un contrôle FormView à le `SimpleDisplay.aspx` aire de conception de la page. Le FormView affiche initialement en tant qu’un bloc gris, nous informe que nous devons fournir, au minimum, d' un contrôle `ItemTemplate`.


[![Le FormView doit inclure un ItemTemplate](displaying-data-with-the-objectdatasource-cs/_static/image46.png)](displaying-data-with-the-objectdatasource-cs/_static/image45.png)

**Figure 17**: le FormView doit inclure un `ItemTemplate` ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image47.png))


Vous pouvez lier le contrôle FormView directement à un contrôle de source de données par le biais balise du FormView, qui crée une valeur par défaut `ItemTemplate` automatiquement (avec un `EditItemTemplate` et `InsertItemTemplate`, si le contrôle de ObjectDatatSource `InsertMethod` et `UpdateMethod` propriétés sont définies). Toutefois, pour cet exemple nous allons lier les données sur le FormView et spécifier son `ItemTemplate` manuellement. Commencez par définir les FormView `DataSourceID` propriété le `ID` du contrôle ObjectDataSource, `ObjectDataSource1`. Ensuite, créez le `ItemTemplate` afin qu’elle affiche le nom du produit et les prix dans une `<h4>` élément et les noms de catégorie et expéditeur inférieur dans la taille de police.


[!code-aspx[Main](displaying-data-with-the-objectdatasource-cs/samples/sample6.aspx)]


[![Le premier produit (Tran) s’affiche dans un Format personnalisé](displaying-data-with-the-objectdatasource-cs/_static/image49.png)](displaying-data-with-the-objectdatasource-cs/_static/image48.png)

**La figure 18**: le premier produit (Tran) s’affiche dans un Format personnalisé ([cliquez pour afficher l’image en taille réelle](displaying-data-with-the-objectdatasource-cs/_static/image50.png))


Le `<%# Eval(propertyName) %>` est la syntaxe de liaison de données. Le `Eval` méthode retourne la valeur de la propriété spécifiée pour l’objet actuel qui est liée au contrôle FormView. Consultez l’article de d’Homère Alex [simplifié et étendus syntaxe liaison de données dans ASP.NET 2.0](http://www.15seconds.com/issue/040630.htm) pour plus d’informations sur les secrets de liaison de données.

Comme le contrôle DetailsView, le FormView affiche uniquement le premier enregistrement retourné par ObjectDataSource. Vous pouvez activer la pagination dans le FormView pour autoriser les visiteurs parcourir les produits d’un à la fois.

## <a name="summary"></a>Récapitulatif

Accès et affichage des données à partir d’une couche de logique métier peuvent être accomplies sans écrire une ligne de code contrôle ObjectDataSource de ASP.NET 2.0. ObjectDataSource appelle une méthode spécifiée d’une classe et retourne les résultats. Ces résultats peuvent être affichés dans un contrôle Web qui est lié à ObjectDataSource de données. Dans ce didacticiel, nous avons au niveau de la liaison des contrôles GridView, DetailsView et FormView de ObjectDataSource.

Jusqu'à présent nous avons vu seulement l’utilisation de ObjectDataSource pour appeler une méthode sans paramètre, mais que se passe-t-il si nous voulons appeler une méthode qui attend des paramètres d’entrée, tels que les `ProductBLL` de classe `GetProductsByCategoryID(categoryID)`? Pour pouvoir appeler une méthode qui attend un ou plusieurs paramètres, nous devons configurer ObjectDataSource pour spécifier les valeurs de ces paramètres. Nous verrons comment procéder dans notre [didacticiel suivant](declarative-parameters-cs.md).

Bonne programmation !

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Créer vos propres contrôles de Source de données](https://msdn.microsoft.com/library/ms364049.aspx)
- [Exemples de GridView pour ASP.NET 2.0](https://msdn.microsoft.com/library/aa479339.aspx)
- [Simplifié et étendues de liaison de données syntaxe dans ASP.NET 2.0](http://www.15seconds.com/issue/040630.htm)
- [Thèmes dans ASP.NET 2.0](http://www.odetocode.com/Articles/423.aspx)
- [Styles de côté serveur à l’aide de thèmes](https://quickstarts.asp.net/quickstartv20/aspnet/doc/themes/stylesheettheme.aspx)
- [Comment : Appliquer des thèmes ASP.NET par programmation](https://msdn.microsoft.com/library/tx35bd89.aspx)

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Hilton Giesenow. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Next](declarative-parameters-cs.md)
