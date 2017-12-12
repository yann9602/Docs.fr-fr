---
uid: web-forms/overview/data-access/enhancing-the-gridview/adding-a-gridview-column-of-radio-buttons-vb
title: "Ajout d’une colonne de GridView de cases d’option (VB) | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel explique comment ajouter une colonne de cases d’option à un contrôle GridView pour fournir un moyen plus intuitif de la sélection d’une ligne unique de l’utilisateur..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/06/2007
ms.topic: article
ms.assetid: 2e31b60b-8723-4f14-b7ee-37859454dc3b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/enhancing-the-gridview/adding-a-gridview-column-of-radio-buttons-vb
msc.type: authoredcontent
ms.openlocfilehash: 9d34fb64984313e5e2844d36a70f3ab08560654e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-a-gridview-column-of-radio-buttons-vb"></a>Ajout d’une colonne de GridView de cases d’option (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_51_VB.exe) ou [télécharger le PDF](adding-a-gridview-column-of-radio-buttons-vb/_static/datatutorial51vb1.pdf)

> Ce didacticiel explique comment ajouter une colonne de cases d’option à un contrôle GridView à fournir un moyen plus intuitive de la sélection d’une seule ligne du contrôle GridView à l’utilisateur.


## <a name="introduction"></a>Introduction

Le contrôle GridView offre un large éventail de fonctionnalités intégrées. Il inclut un nombre de champs différents pour l’affichage de texte, des images, des liens hypertexte et des boutons. Il prend en charge les modèles de davantage de personnalisation. En quelques clics de souris, elle s possible pour rendre un GridView où chaque ligne peut être sélectionnée via un bouton, ou pour activer la modification ou la suppression de fonctionnalités. En dépit de la multitude de fonctionnalités fournies, il arrive souvent que les situations dans lesquelles supplémentaires, fonctionnalités non prises en charge doivent être ajoutés. Dans ce didacticiel et les deux, nous allons examiner comment améliorer les fonctionnalités de s GridView pour inclure des fonctionnalités supplémentaires.

Ce didacticiel et la suivante se concentrent sur l’amélioration du processus de sélection de ligne. Comme examinés dans le [maître/détail à l’aide d’un GridView maître avec un DetailView détail](../masterdetail/master-detail-using-a-selectable-master-gridview-with-a-details-detailview-vb.md), nous pouvons ajouter un CommandField au GridView qui inclut un bouton de sélection. Lorsque vous cliquez dessus, une publication (postback) s’ensuit et s GridView `SelectedIndex` propriété est mise à jour à l’index de la ligne dont bouton Sélectionner l’utilisateur a cliqué. Dans le [maître/détail à l’aide d’un GridView maître avec un DetailView détail](../masterdetail/master-detail-using-a-selectable-master-gridview-with-a-details-detailview-vb.md) didacticiel, nous avons vu comment utiliser cette fonctionnalité pour afficher les détails de la ligne GridView sélectionnée.

Bien que le bouton de sélection fonctionne dans de nombreuses situations, il peuvent ne pas fonctionne aussi pour d’autres. Au lieu d’utiliser un bouton, deux autres éléments d’interface utilisateur sont utilisés pour la sélection : la case d’option et la case à cocher. Nous pouvons améliorer le GridView afin qu’au lieu d’un bouton de sélection, chaque ligne contient une case d’option ou une case à cocher. Dans les scénarios où l’utilisateur peut sélectionner uniquement un des enregistrements de GridView, la case d’option a priorité sur le bouton de sélection. Dans les situations où l’utilisateur peut sélectionner de potentiellement plusieurs enregistrements comme dans une application de messagerie électronique basé sur le web, où un utilisateur peut souhaiter sélectionner plusieurs messages à supprimer de la case à cocher offre une fonctionnalité qui n’est pas disponible à partir du bouton de sélection ou d’une case d’option interfaces utilisateur.

Ce didacticiel explique comment ajouter une colonne de cases d’option dans le GridView. Le didacticiel continuer explore à l’aide de cases à cocher.

## <a name="step-1-creating-the-enhancing-the-gridview-web-pages"></a>Étape 1 : Création de l’amélioration de Pages Web de GridView

Avant de commencer à améliorer le contrôle GridView pour inclure une colonne de boutons radio, permettent de s Prenez tout d’abord créer les pages ASP.NET dans notre projet de site Web que nous aurons besoin pour ce didacticiel et les deux. Commencez par ajouter un nouveau dossier nommé `EnhancedGridView`. Ensuite, ajoutez les pages ASP.NET suivantes à ce dossier, en veillant à associer chaque page avec le `Site.master` page maître :

- `Default.aspx`
- `RadioButtonField.aspx`
- `CheckBoxField.aspx`
- `InsertThroughFooter.aspx`


![Ajouter les Pages ASP.NET pour les didacticiels de SqlDataSource](adding-a-gridview-column-of-radio-buttons-vb/_static/image1.gif)

**Figure 1**: ajouter les Pages ASP.NET pour les didacticiels de SqlDataSource


Comme dans les autres dossiers, `Default.aspx` dans le `EnhancedGridView` dossier répertorie les didacticiels dans sa section. N’oubliez pas que le `SectionLevelTutorialListing.ascx` contrôle utilisateur fournit cette fonctionnalité. Par conséquent, ajoutez ce contrôle utilisateur pour `Default.aspx` en le faisant glisser à partir de l’Explorateur de solutions sur la page s mode Création.


[![Ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx](adding-a-gridview-column-of-radio-buttons-vb/_static/image2.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image1.png)

**Figure 2**: ajouter la `SectionLevelTutorialListing.ascx` contrôle utilisateur à `Default.aspx` ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image2.png))


Enfin, ajoutez ces quatre pages en tant qu’entrées pour les `Web.sitemap` fichier. En particulier, ajoutez le balisage suivant après l’utilisation du contrôle SqlDataSource `<siteMapNode>`:


[!code-xml[Main](adding-a-gridview-column-of-radio-buttons-vb/samples/sample1.xml)]

Après la mise à jour `Web.sitemap`, prenez un moment pour afficher le site Web des didacticiels via un navigateur. Le menu de gauche inclut désormais les éléments pour la modification, l’insertion et la suppression des didacticiels.


![Le plan de Site inclut maintenant des entrées pour l’amélioration des didacticiels GridView](adding-a-gridview-column-of-radio-buttons-vb/_static/image3.gif)

**Figure 3**: le plan de Site inclut maintenant des entrées pour l’amélioration des didacticiels GridView


## <a name="step-2-displaying-the-suppliers-in-a-gridview"></a>Étape 2 : Afficher les fournisseurs dans un GridView

Pour ce didacticiel s permettre de générer un GridView qui répertorie les fournisseurs à partir des États-Unis, chaque ligne GridView en fournissant une case d’option. Après avoir sélectionné un fournisseur via le bouton radio, l’utilisateur peut afficher les produits de s fournisseur en cliquant sur un bouton. Alors que cette tâche peut sembler triviale, il existe un certain nombre de subtilités qui la rendent particulièrement difficile. Avant d’aborder ces subtilités, permettent de s tout d’abord obtenir une liste des fournisseurs de GridView.

Commencez par ouvrir le `RadioButtonField.aspx` page dans le `EnhancedGridView` dossier en faisant glisser un contrôle GridView à partir de la boîte à outils vers le concepteur. Définir le contrôle GridView s `ID` à `Suppliers` et, à partir de sa balise active, choisissez de créer une nouvelle source de données. En particulier, créez un ObjectDataSource nommé `SuppliersDataSource` qui tire ses données à partir de la `SuppliersBLL` objet.


[![Créer un nouveau ObjectDataSource nommé SuppliersDataSource](adding-a-gridview-column-of-radio-buttons-vb/_static/image4.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image3.png)

**Figure 4**: créer un nouveau nommé de ObjectDataSource `SuppliersDataSource` ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image4.png))


[![Configurer pour utiliser la classe SuppliersBLL ObjectDataSource](adding-a-gridview-column-of-radio-buttons-vb/_static/image5.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image5.png)

**Figure 5**: configurer l’ObjectDataSource à utiliser le `SuppliersBLL` classe ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image6.png))


Étant donné que nous voulons uniquement répertorier les fournisseurs aux États-Unis d’Amérique, choisissez la `GetSuppliersByCountry(country)` méthode dans la liste déroulante dans l’onglet sélection.


[![Configurer pour utiliser la classe SuppliersBLL ObjectDataSource](adding-a-gridview-column-of-radio-buttons-vb/_static/image6.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image7.png)

**Figure 6**: configurer l’ObjectDataSource à utiliser le `SuppliersBLL` classe ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image8.png))


À partir de l’onglet mise à jour, sélectionnez (aucun), puis cliquez sur Suivant.


[![Configurer pour utiliser la classe SuppliersBLL ObjectDataSource](adding-a-gridview-column-of-radio-buttons-vb/_static/image7.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image9.png)

**Figure 7**: configurer l’ObjectDataSource à utiliser le `SuppliersBLL` classe ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image10.png))


Étant donné que la `GetSuppliersByCountry(country)` méthode accepte un paramètre, l’Assistant Configurer la Source de données nous demande la source de ce paramètre. Pour spécifier une valeur codée en dur (États-Unis, dans cet exemple), laissez le paramètre source liste déroulante, la valeur None et entrez la valeur par défaut dans la zone de texte. Cliquez sur Terminer pour terminer l’Assistant.


[![Utiliser les États-Unis comme valeur par défaut pour le paramètre de pays](adding-a-gridview-column-of-radio-buttons-vb/_static/image8.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image11.png)

**Figure 8**: États-Unis d’utiliser comme valeur par défaut pour le `country` paramètre ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image12.png))


Après la fin de l’Assistant, le contrôle GridView inclura un BoundField pour chacun des champs de données de fournisseur. Supprimer tout sauf la `CompanyName`, `City`, et `Country` BoundFields et renommer le `CompanyName` BoundFields `HeaderText` propriété au fournisseur. Après cela, la syntaxe déclarative GridView et ObjectDataSource doit ressembler à ce qui suit.


[!code-aspx[Main](adding-a-gridview-column-of-radio-buttons-vb/samples/sample2.aspx)]

Pour ce didacticiel, permettent d’autoriser l’utilisateur d’afficher le fournisseur sélectionné les produits s sur la même page que la liste des fournisseurs, ou sur une autre page s. Pour ce faire, ajoutez deux contrôles bouton à la page. Je ve ensemble la `ID` s de ces deux boutons pour `ListProducts` et `SendToProducts`, avec l’idée que quand `ListProducts` clic est effectué sur une publication (postback) se produit et les produits du fournisseur sélectionné s sont répertoriés sur la même page, mais lorsque `SendToProducts` est activé, l’utilisateur sera whisked à une autre page qui répertorie les produits.

La figure 9 illustre la `Suppliers` GridView et le Web bouton deux contrôles lorsqu’ils sont affichés via un navigateur.


[![Ces fournisseurs à partir des États-Unis ont leur nom, ville et pays les informations répertoriées](adding-a-gridview-column-of-radio-buttons-vb/_static/image9.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image13.png)

**Figure 9**: les fournisseurs à partir des USA ont leurs nom, ville et les informations de pays apparaissent ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image14.png))


## <a name="step-3-adding-a-column-of-radio-buttons"></a>Étape 3 : Ajout d’une colonne de cases d’option

À ce stade le `Suppliers` GridView a trois BoundFields affichant le nom de la société, ville et pays de chaque fournisseur aux États-Unis d’Amérique. Il n’a toujours pas une colonne de cases d’option, toutefois. Malheureusement, le GridView n’inclure un intégrés RadioButtonField, sinon nous peut simplement ajouter à la grille et le faire. Au lieu de cela, nous pouvons ajouter TemplateField et configurer ses `ItemTemplate` pour afficher un bouton radio, ce qui entraîne une case pour chaque ligne GridView.

Au départ, nous pouvons partons du principe que l’interface utilisateur de votre choix peut être implémentée en ajoutant un contrôle RadioButton Web pour le `ItemTemplate` de TemplateField. Cela sera effectivement ajouter une case d’option unique à chaque ligne du contrôle GridView, les cases d’option ne peuvent pas être regroupées et par conséquent, ne sont pas mutuellement exclusives. Autrement dit, un utilisateur final est en mesure de sélectionner plusieurs cases d’option simultanément dans le GridView.

Bien à l’aide de contrôles RadioButton Web TemplateField n’offre pas les fonctionnalités dont nous avons besoin, s permettent d’implémenter cette approche, telle qu’elle s judicieux d’examiner les raisons pour lesquelles les cases d’option résultants ne sont pas regroupées. Commencez par ajouter TemplateField au GridView fournisseurs, rendant le champ plus à gauche. Ensuite, à partir de la balise active de s GridView, cliquez sur le lien Modifier les modèles et faites glisser un contrôle RadioButton Web à partir de la boîte à outils dans le s TemplateField `ItemTemplate` (voir la Figure 10). Définir le RadioButton s `ID` propriété `RowSelector` et `GroupName` propriété `SuppliersGroup`.


[![Ajouter un contrôle RadioButton à ItemTemplate](adding-a-gridview-column-of-radio-buttons-vb/_static/image10.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image15.png)

**La figure 10**: ajouter un contrôle RadioButton à le `ItemTemplate` ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image16.png))


Après avoir apporté ces ajouts via le concepteur, votre balisage de s GridView doit ressembler à ce qui suit :


[!code-aspx[Main](adding-a-gridview-column-of-radio-buttons-vb/samples/sample3.aspx)]

Les s RadioButton [ `GroupName` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.radiobutton.groupname(VS.80).aspx) est celui qui est utilisé pour regrouper une série de cases d’option. Tous les contrôles de case d’option avec le même `GroupName` valeur sont considérées comme groupées ; qu’une case d’option peut être sélectionnée à partir d’un groupe à la fois. Le `GroupName` propriété spécifie la valeur de la case de rendu s `name` attribut. Le navigateur examine les boutons radio `name` attributs pour déterminer la case d’option bouton regroupements.

Avec le contrôle RadioButton Web ajouté à la `ItemTemplate`, visitez cette page via un navigateur, puis cliquez sur les boutons de case d’option dans les lignes de grille s. Notez comment les boutons radio ne sont pas regroupés, ce qui permet de sélectionner toutes les lignes, comme la Figure 11 illustre.


[![Les boutons de case d’option de s GridView ne sont pas regroupés](adding-a-gridview-column-of-radio-buttons-vb/_static/image11.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image17.png)

**Figure 11**: GridView s elles ne sont pas regroupées ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image18.png))


La raison pour laquelle les cases d’option ne sont pas regroupées est, car leur rendu `name` les attributs sont différents, bien qu’ils aient le même `GroupName` paramètre de propriété. Pour visualiser ces différences, effectuez l’une vue/Source à partir du navigateur et examinez le balisage du bouton radio :


[!code-html[Main](adding-a-gridview-column-of-radio-buttons-vb/samples/sample4.html)]

Notez comment les deux le `name` et `id` attributs ne sont pas les valeurs exactes, tel que spécifié dans la fenêtre Propriétés, mais sont précédés d’un nombre d’autres `ID` valeurs. Supplémentaires `ID` les valeurs ajoutées au début du rendu `id` et `name` attributs sont la `ID` s de la case d’option boutons contrôles parents le `GridViewRow` s `ID` s, s GridView `ID`, le Contenu du contrôle s `ID`et le formulaire Web de s `ID`. Ces `ID` s sont ajoutés afin que chaque rendue contrôle Web dans le GridView a unique `id` et `name` valeurs.

Rendus doivent une autre `name` et `id` , car il s’agit comment le navigateur identifie de façon unique chaque contrôle sur la côté client et comment il identifie sur le serveur web action ou de modification s’est produite lors de la publication. Par exemple, supposons que nous voulons exécuter du code côté serveur chaque fois qu’un s RadioButton vérifiée état a été modifié. Pour ce faire, nous avons définissant le RadioButton s `AutoPostBack` propriété `True` et la création d’un gestionnaire d’événements pour le `CheckChanged` événement. Toutefois, si le rendu `name` et `id` des valeurs pour tous les boutons radio étaient les mêmes sur publication (postback) Impossible de déterminer quel spécifiques RadioButton à l’utilisateur a cliqué.

Court de celui-ci est que nous ne pouvons pas créer une colonne de cases d’option dans un contrôle GridView à l’aide du contrôle RadioButton Web. Au lieu de cela, nous devons utiliser plutôt archaïques techniques pour vous assurer que le balisage approprié est injecté dans chaque ligne GridView.

> [!NOTE]
> Comme le contrôle RadioButton Web, le bouton radio lorsqu’il est ajouté à un modèle de contrôle HTML inclura uniques `name` attribut, qui effectue les cases d’option dans la grille non groupée. Si vous n’êtes pas familiarisé avec les contrôles HTML, n’hésitez pas à ignorer cette remarque, comme les contrôles HTML sont rarement utilisées, en particulier dans ASP.NET 2.0. Mais si vous souhaitez en savoir plus, consultez [K. Scott Allen](http://odetocode.com/blogs/scott/default.aspx) une entrée de blog s [les contrôles Web et les contrôles HTML](http://www.odetocode.com/Articles/348.aspx).


## <a name="using-a-literal-control-to-inject-radio-button-markup"></a>À l’aide d’un contrôle Literal pour injecter du balisage du bouton Radio

Pour correctement regrouper tous les boutons de case d’option dans le GridView, nous devons manuellement injecter le balisage de boutons de case d’option dans le `ItemTemplate`. Chaque bouton radio a besoin de la même `name` d’attribut, mais doit avoir une valeur unique `id` attribut (au cas où vous souhaitez accéder à un bouton radio via un script côté client). Une fois un utilisateur sélectionne un bouton radio et la page effectue une publication, le navigateur sera renvoyer la valeur de la case d’option sélectionnée s `value` attribut. Par conséquent, chaque case d’option devez unique `value` attribut. Enfin, sur la publication (postback) nous devons Assurez-vous d’ajouter le `checked` attribut à l’une case est activée, sinon une fois que l’utilisateur effectue une sélection et les billets de retour, les boutons radio retournera à leur état par défaut (tous les non sélectionnées).

Il existe deux approches qui peuvent être prises pour injecter du balisage de bas niveau dans un modèle. Une consiste à effectuer une combinaison de balisage et les appels aux méthodes définies dans la classe code-behind de la mise en forme. Cette technique est abordée dans le [à l’aide de TemplateField dans le contrôle GridView](../custom-formatting/using-templatefields-in-the-gridview-control-vb.md) didacticiel. Dans notre cas, il peut ressembler à :


[!code-aspx[Main](adding-a-gridview-column-of-radio-buttons-vb/samples/sample5.aspx)]

Ici, `GetUniqueRadioButton` et `GetRadioButtonValue` seraient des méthodes définies dans la classe code-behind qui retourné approprié `id` et `value` valeurs d’attributs pour chaque case d’option. Cette approche fonctionne bien pour l’affectation de la `id` et `value` des attributs, mais n’était pas obligé de spécifier le `checked` valeur d’attribut, car la syntaxe de liaison de données est exécutée uniquement lorsque des données sont tout d’abord liées au GridView. Par conséquent, si le contrôle GridView présente l’état d’affichage, les méthodes de mise en forme ne se déclenche lorsque la page est chargée (ou lorsque le contrôle GridView est explicitement reliée à la source de données) et par conséquent, la fonction qui définit le `checked` attribut a gagné t être appelée sur publication (postback). Il s un problème subtil plutôt et un peu dépasse le cadre de cet article, donc je vais laisser ce. Je faire, toutefois, vous encourageons à essayer d’utiliser l’approche ci-dessus et par le biais de travail au point où vous allez être bloquée. Alors que cet un t exercice a gagné vous aider à toute une valeur plus proche vers une version de travail, il vous aide à favoriser l’émergence d’une meilleure compréhension du GridView et le cycle de vie de la liaison de données.

L’autre approche pour injecter personnalisé, balisage de bas niveau d’un modèle et l’approche que nous utiliserons pour ce didacticiel consiste à ajouter un [contrôle Literal](https://msdn.microsoft.com/en-us/library/sz4949ks(VS.80).aspx) au modèle. Puis, dans le GridView/s `RowCreated` ou `RowDataBound` Gestionnaire d’événements, le contrôle Literal sont accessibles par programme et ses `Text` propriété définie au balisage à émettre.

Commencez par supprimer le contrôle RadioButton à partir de la s TemplateField `ItemTemplate`, remplaçant par un contrôle Literal. Définir le contrôle de littéral s `ID` à `RadioButtonMarkup`.


[![Ajouter un contrôle littéral à ItemTemplate](adding-a-gridview-column-of-radio-buttons-vb/_static/image12.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image19.png)

**Figure 12**: ajouter un contrôle littéral à la `ItemTemplate` ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image20.png))


Ensuite, créez un gestionnaire d’événements pour le contrôle GridView s `RowCreated` événement. Le `RowCreated` événement est déclenché une fois pour chaque ligne ajoutée, ou non les données sont en cours de nouveau liées au GridView. Cela signifie que même sur une publication (postback) lorsque les données sont rechargées à partir de l’état d’affichage, la `RowCreated` événement est toujours déclenché et c’est pourquoi nous utilisons à la place de `RowDataBound` (qui déclenche uniquement lorsque les données sont liées explicitement pour les données de contrôle Web).

Dans ce gestionnaire d’événements, nous voulons uniquement effectuée si nous re traiter avec une ligne de données. Pour chaque ligne de données que vous souhaitez référencer par programme le `RadioButtonMarkup` contrôle Literal et définissez son `Text` propriété au balisage à émettre. Comme le montre le code suivant, le balisage émis crée une case d’option bouton dont `name` attribut a la valeur `SuppliersGroup`, dont `id` attribut a la valeur `RowSelectorX`, où *X* est l’index de la ligne GridView, et dont `value` attribut est défini sur l’index de la ligne GridView.


[!code-vb[Main](adding-a-gridview-column-of-radio-buttons-vb/samples/sample6.vb)]

Lorsqu’une ligne GridView est sélectionnée et qu’une publication (postback) se produit, nous sommes intéressés par la `SupplierID` du fournisseur sélectionné. Par conséquent, on pourrait penser que la valeur de chaque case d’option doit être le réel `SupplierID` (au lieu de l’index de la ligne GridView). Cela peut fonctionner dans certaines circonstances, il serait un risque de sécurité pour accepter et traiter de manière aveugle une `SupplierID`. Notre GridView, par exemple, répertorie uniquement les fournisseurs aux États-Unis d’Amérique. Toutefois, si le `SupplierID` est passée directement à partir de la case, Nouveautés pour arrêter un utilisateur partir de manipuler le `SupplierID` valeur envoyée dans la publication (postback) ? À l’aide de l’index de ligne en tant que le `value`et l’obtention de la `SupplierID` lors de la publication à partir de la `DataKeys` collection, nous pouvons vous assurer que l’utilisateur est uniquement à l’aide d’une de la `SupplierID` valeurs associées à une des lignes GridView.

Après avoir ajouté ce code de gestionnaire d’événements, prenez une minute pour tester la page dans un navigateur. Notez d’abord, cette case d’option qu’un seul bouton dans la grille peut être sélectionné à la fois. Toutefois, quand en sélectionnant une case d’option et en cliquant sur un des boutons, une publication (postback) se produit et les boutons de case d’option tous les restaurer à leur état initial (autrement dit, lors de la publication, la case d’option sélectionnée est n’est plus sélectionnée). Pour résoudre ce problème, nous avons besoin d’augmenter la `RowCreated` Gestionnaire d’événements pour qu’il inspecte l’index du bouton de case d’option sélectionnée envoyé à partir de la publication (postback) et ajoute le `checked="checked"` le balisage émis de l’index de ligne correspond à l’attribut.

Lorsqu’une publication (postback) se produit, le navigateur envoie le `name` et `value` de la case d’option sélectionnée. La valeur peut être récupérée par programmation à l’aide de `Request.Form("name")`. Le [ `Request.Form` propriété](https://msdn.microsoft.com/en-us/library/system.web.httprequest.form.aspx) fournit un [ `NameValueCollection` ](https://msdn.microsoft.com/en-us/library/system.collections.specialized.namevaluecollection.aspx) représentant les variables de formulaire. Les variables de formulaire sont les noms et valeurs des champs de formulaire dans la page web et renvoyés par le navigateur web chaque fois qu’une publication (postback) s’ensuit. Étant donné que le rendu `name` attribut des cases d’option dans le GridView est `SuppliersGroup`, lorsque la page web est publiée le navigateur envoie `SuppliersGroup=valueOfSelectedRadioButton` sur le serveur web (ainsi que les autres champs de formulaire). Cette information est ensuite accessible à partir de la `Request.Form` à l’aide de la propriété : `Request.Form("SuppliersGroup")`.

Depuis que nous allons besoin afin de déterminer la case d’option sélectionnée d’index pas uniquement dans le `RowCreated` Gestionnaire d’événements, mais dans le `Click` ajouter des gestionnaires d’événements pour les contrôles de bouton Web, permettent de s un `SuppliersSelectedIndex` propriété à la classe code-behind qui renvoie `-1`si aucune case d’option a été sélectionnée et l’index sélectionné si un des boutons de case d’option est sélectionné.


[!code-vb[Main](adding-a-gridview-column-of-radio-buttons-vb/samples/sample7.vb)]

Avec cette propriété est ajoutée, nous savons que pour ajouter le `checked="checked"` balisage dans le `RowCreated` Gestionnaire d’événements lorsque `SuppliersSelectedIndex` est égal à `e.Row.RowIndex`. Mettre à jour le Gestionnaire d’événements pour inclure cette logique :


[!code-vb[Main](adding-a-gridview-column-of-radio-buttons-vb/samples/sample8.vb)]

Avec cette modification, la case d’option sélectionnée est sélectionnée après une publication (postback). Maintenant que nous avons la possibilité de spécifier les boutons de case d’option sont sélectionnée, nous pouvons modifier le comportement afin que lorsque la page a été visitée tout d’abord, la première case ligne s GridView a été sélectionnée (plutôt que d’avoir aucun cases d’option sélectionnée par défaut, qui est en cours comportement). Pour que le premier bouton de case d’option sélectionné par défaut, il suffit de changer le `If SuppliersSelectedIndex = e.Row.RowIndex Then` instruction à la suivante : `If SuppliersSelectedIndex = e.Row.RowIndex OrElse (Not Page.IsPostBack AndAlso e.Row.RowIndex = 0) Then`.

À ce stade, nous avons ajouté une colonne de cases d’option groupées au GridView qui autorise une seule ligne GridView sélectionné et conservés entre les publications (postback). Les étapes suivantes sont à afficher les produits fournis par le fournisseur sélectionné. À l’étape 4 nous verrons comment rediriger l’utilisateur vers une autre page, envoyer le long de l’élément `SupplierID`. À l’étape 5, nous verrons comment afficher les produits de s fournisseur sélectionné dans un GridView sur la même page.

> [!NOTE]
> Au lieu d’utiliser TemplateField (le focus de cette étape 3 longues), nous pouvons créer un personnalisé `DataControlField` classe qui rend la fonctionnalité et l’interface utilisateur appropriée. Le [ `DataControlField` classe](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.datacontrolfield.aspx) est la classe de base dont dérivent le BoundField, CheckBoxField, TemplateField et autres champs intégrés de GridView et DetailsView. Création d’un fichier `DataControlField` classe signifie que la colonne de cases d’option peut être ajoutée uniquement à l’aide de la syntaxe déclarative et rend également répliquent la fonctionnalité sur les autres pages web et d’autres applications web beaucoup plus faciles.


Si vous avez déjà créé personnalisé, compilé contrôles dans ASP.NET, cependant, vous savez que cela nécessite une grande quantité de gros du travail et s’accompagne d’un hôte de subtilités et de cas qui doivent être gérés avec précaution. Par conséquent, nous ne prend pas mise en œuvre d’une colonne de cases d’option comme `DataControlField` de classe pour l’instant et coller avec l’option TemplateField. Nous allons peut-être la possibilité d’Explorer la création et à l’aide de déploiement personnalisé `DataControlField` classes dans un didacticiel futures !

## <a name="step-4-displaying-the-selected-supplier-s-products-in-a-separate-page"></a>Étape 4 : Afficher les produits de s fournisseur sélectionné dans une Page distincte

Une fois que l’utilisateur a sélectionné une ligne GridView, nous devons afficher les produits de s le fournisseur sélectionné. Dans certains cas, nous pouvons adopter afficher ces produits dans une page distincte, dans d’autres que préfèrent nous pourrions le faire dans la même page. Permettent de s tout d’abord examiner comment afficher les produits dans une page distincte ; à l’étape 5 nous allons nous intéresser à l’ajout d’un contrôle GridView à `RadioButtonField.aspx` pour afficher les produits du fournisseur sélectionné s.

Actuellement, il existe deux contrôles bouton Web sur la page `ListProducts` et `SendToProducts`. Lorsque le `SendToProducts` bouton est activé, vous souhaitez envoyer à l’utilisateur `~/Filtering/ProductsForSupplierDetails.aspx`. Cette page a été créée dans le [filtrage de maître/détail entre les deux Pages](../masterdetail/master-detail-filtering-across-two-pages-vb.md) didacticiel et affiche les produits pour le fournisseur dont `SupplierID` est passée via le champ de chaîne de requête nommé `SupplierID`.

Pour fournir cette fonctionnalité, créez un gestionnaire d’événements pour le `SendToProducts` bouton s `Click` événement. À l’étape 3, nous avons ajouté la `SuppliersSelectedIndex` propriété qui retourne l’index de la ligne dont case d’option est sélectionnée. Correspondants `SupplierID` peuvent être récupérés à partir de la s GridView `DataKeys` collection et l’utilisateur peuvent ensuite être envoyées à `~/Filtering/ProductsForSupplierDetails.aspx?SupplierID=SupplierID` à l’aide de `Response.Redirect("url")`.


[!code-vb[Main](adding-a-gridview-column-of-radio-buttons-vb/samples/sample9.vb)]

Ce code fonctionne très tant qu’un des boutons de case d’option est sélectionné dans le GridView. Si, au départ, le contrôle GridView ne dispose pas des boutons de case d’option sélectionnées, et que l’utilisateur clique sur le `SendToProducts` bouton, `SuppliersSelectedIndex` sera `-1`, ce qui entraîne une exception levée depuis `-1` est en dehors de la plage d’index de la `DataKeys`collection. Cela n’est pas un critère important, toutefois, si vous avez décidé de mettre à jour le `RowCreated` Gestionnaire d’événements comme indiqué à l’étape 3 afin de disposer de la première case dans le GridView sélectionné par défaut.

Pour prendre en charge un `SuppliersSelectedIndex` valeur `-1`, ajoutez un contrôle Web Label à la page au-dessus du GridView. Définir son `ID` propriété `ChooseSupplierMsg`, ses `CssClass` propriété `Warning`, ses `EnableViewState` et `Visible` propriétés `False`et son `Text` propriété Veuillez choisir un fournisseur à partir de la grille. La classe CSS `Warning` affiche le texte dans une police rouge, gras, italique volumineuse et est défini dans `Styles.css`. En définissant le `EnableViewState` et `Visible` propriétés `False`, l’étiquette n’est pas rendue à l’exception pour uniquement les publications où le contrôle s `Visible` propriété est définie par programme `True`.


[![Ajouter un contrôle étiquette au-dessus du contrôle GridView](adding-a-gridview-column-of-radio-buttons-vb/_static/image13.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image21.png)

**Figure 13**: ajouter une étiquette Web contrôle ci-dessus GridView ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image22.png))


Ensuite, augmenter la `Click` Gestionnaire d’événements pour afficher les `ChooseSupplierMsg` étiquette si `SuppliersSelectedIndex` est inférieur à zéro et rediriger l’utilisateur à `~/Filtering/ProductsForSupplierDetails.aspx?SupplierID=SupplierID` dans le cas contraire.


[!code-vb[Main](adding-a-gridview-column-of-radio-buttons-vb/samples/sample10.vb)]

Visitez la page dans un navigateur et d’un clic sur le `SendToProducts` bouton avant de sélectionner un fournisseur dans le GridView. Comme le montre la Figure 14, cela permet d’afficher le `ChooseSupplierMsg` étiquette. Ensuite, sélectionnez un fournisseur et cliquez sur le `SendToProducts` bouton. Cela sera vous tous à une page qui répertorie les produits fournis par le fournisseur sélectionné. La figure 15 montre la `ProductsForSupplierDetails.aspx` page lorsque le fournisseur Bigfoot brasseries a été sélectionné.


[![L’étiquette ChooseSupplierMsg s’affiche si aucun fournisseur n’est sélectionnée](adding-a-gridview-column-of-radio-buttons-vb/_static/image14.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image23.png)

**La figure 14**: le `ChooseSupplierMsg` étiquette est affichée si aucun fournisseur n’est sélectionnée ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image24.png))


[![Les produits de s sélectionné un fournisseur sont affichés dans ProductsForSupplierDetails.aspx](adding-a-gridview-column-of-radio-buttons-vb/_static/image15.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image25.png)

**Figure 15**: le fournisseur sélectionné s produits sont affichent dans `ProductsForSupplierDetails.aspx` ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image26.png))


## <a name="step-5-displaying-the-selected-supplier-s-products-on-the-same-page"></a>Étape 5 : Afficher les produits de s fournisseur sélectionné sur la même Page

À l’étape 4, nous avons vu comment envoyer des produits de s à l’utilisateur vers une autre page web pour afficher le fournisseur sélectionné. Vous pouvez également produits s fournisseur sélectionné peuvent être affichés sur la même page. Pour illustrer cela, nous allons ajouter un autre contrôle GridView à `RadioButtonField.aspx` pour afficher les produits du fournisseur sélectionné s.

Étant donné que nous voulons uniquement cette GridView de produits pour afficher une fois qu’un fournisseur a été sélectionné, ajoutez un contrôle de panneau de configuration Web sous le `Suppliers` GridView, en définissant ses `ID` à `ProductsBySupplierPanel` et son `Visible` propriété `False`. Dans le panneau de configuration, ajoutez le texte de produits pour le fournisseur sélectionné, suivie d’un GridView nommé `ProductsBySupplier`. À partir de la balise active de s GridView, choisissez de lier à un nouveau ObjectDataSource nommé `ProductsBySupplierDataSource`.


[![Lier le contrôle ProductsBySupplier GridView à un nouveau ObjectDataSource](adding-a-gridview-column-of-radio-buttons-vb/_static/image16.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image27.png)

**Figure 16**: lier le `ProductsBySupplier` contrôle GridView à une nouvelle ObjectDataSource ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image28.png))


Ensuite, configurez l’ObjectDataSource à utiliser le `ProductsBLL` classe. Étant donné que nous voulons uniquement récupérer ces produits fournis par le fournisseur sélectionné, spécifiez que ObjectDataSource doit appeler le `GetProductsBySupplierID(supplierID)` méthode pour récupérer ses données. Sélectionnez (aucun) dans la liste déroulante dans la mise à jour, insérer et supprimer des onglets.


[![Configurer pour utiliser la méthode GetProductsBySupplierID(supplierID) ObjectDataSource](adding-a-gridview-column-of-radio-buttons-vb/_static/image17.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image29.png)

**Figure 17**: configurer l’ObjectDataSource à utiliser le `GetProductsBySupplierID(supplierID)` (méthode) ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image30.png))


[![Définir les listes déroulantes (None) dans la mise à jour, insérer et supprimer des onglets](adding-a-gridview-column-of-radio-buttons-vb/_static/image18.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image31.png)

**La figure 18**: définir les listes déroulantes (None) dans la mise à jour, insérer et supprimer des onglets ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image32.png))


Après avoir configuré l’instruction SELECT, mettre à jour, insérer et supprimer des onglets, cliquez sur Suivant. Étant donné que la `GetProductsBySupplierID(supplierID)` méthode attend un paramètre d’entrée, l’Assistant créer une Source de données vous êtes invité à spécifier la source pour la valeur du paramètre s.

Nous avons deux options ici, en spécifiant la source de la valeur du paramètre s. Nous pouvons utiliser l’objet de paramètre par défaut et assigner par programme la valeur de la `SuppliersSelectedIndex` propriété pour le paramètre s `DefaultValue` propriété dans le s ObjectDataSource `Selecting` Gestionnaire d’événements. Faire référence à la [définition par programme des valeurs de paramètres de l’ObjectDataSource](../basic-reporting/programmatically-setting-the-objectdatasource-s-parameter-values-vb.md) didacticiel pour un petit rappel sur l’assignation par programme des valeurs aux paramètres s ObjectDataSource.

Ou bien, nous pouvons utiliser un ControlParameter et faire référence à la `Suppliers` GridView s [ `SelectedValue` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.selectedvalue.aspx) (voir Figure 19). Les s GridView `SelectedValue` propriété retourne le `DataKey` valeur correspondant à la [ `SelectedIndex` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.selectedindex.aspx). Afin que cette option fonctionne, nous devons définir par programme le GridView s `SelectedIndex` propriété sélectionné ligne lorsque la `ListProducts` du bouton. Avantage, en définissant le `SelectedIndex`, l’enregistrement sélectionné effectuera sur le `SelectedRowStyle` défini dans le `DataWebControls` thème (un arrière-plan jaune).


[![Utilisez un ControlParameter pour spécifier des GridView s SelectedValue comme Source du paramètre](adding-a-gridview-column-of-radio-buttons-vb/_static/image19.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image33.png)

**Figure 19**: utiliser un ControlParameter pour spécifier le s GridView SelectedValue comme Source du paramètre ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image34.png))


À la fin de l’Assistant, Visual Studio ajoute automatiquement des champs pour les champs de données produit s. Supprimer tout sauf la `ProductName`, `CategoryName`, et `UnitPrice` BoundFields et modifier les `HeaderText` propriétés pour le produit, la catégorie et prix. Configurer le `UnitPrice` BoundField afin que sa valeur est mise en forme comme une valeur monétaire. Après avoir apporté ces modifications, le balisage déclaratif s ObjectDataSource, GridView et panneau de configuration doit se présenter comme suit :


[!code-aspx[Main](adding-a-gridview-column-of-radio-buttons-vb/samples/sample11.aspx)]

Pour terminer cet exercice, nous devons définir le GridView s `SelectedIndex` propriété le `SelectedSuppliersIndex` et le `ProductsBySupplierPanel` panneau s `Visible` propriété `True` lorsque le `ListProducts` bouton. Pour ce faire, créez un gestionnaire d’événements pour le `ListProducts` le contrôle bouton Web s `Click` événement et ajoutez le code suivant :


[!code-vb[Main](adding-a-gridview-column-of-radio-buttons-vb/samples/sample12.vb)]

Si un fournisseur n’a pas été sélectionné dans le GridView, le `ChooseSupplierMsg` étiquette est affichée et `ProductsBySupplierPanel` panneau masqué. Sinon, si un fournisseur a été sélectionné, le `ProductsBySupplierPanel` s’affiche et le s GridView `SelectedIndex` propriété est mise à jour.

Figure 20 montre les résultats une fois que le fournisseur Bigfoot brasseries a été sélectionné et les produits à afficher sur le bouton de Page a été activé.


[![Les produits fournis par Bigfoot brasseries sont répertoriés sur la même Page](adding-a-gridview-column-of-radio-buttons-vb/_static/image20.gif)](adding-a-gridview-column-of-radio-buttons-vb/_static/image35.png)

**Figure 20**: les produits fournis par Bigfoot brasseries sont répertoriés sur la même Page ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-radio-buttons-vb/_static/image36.png))


## <a name="summary"></a>Résumé

Comme indiqué dans le [maître/détail à l’aide d’un GridView maître avec un DetailView détail](../masterdetail/master-detail-using-a-selectable-master-gridview-with-a-details-detailview-vb.md) didacticiel, les enregistrements peuvent être sélectionnés à partir d’un contrôle GridView à l’aide d’un CommandField dont `ShowSelectButton` est définie sur `True`. Mais le CommandField affiche ses boutons sous forme de boutons de commande standards, des liens ou des images. Une interface utilisateur de sélection de ligne autre doit fournir une case d’option ou une case à cocher de chaque ligne GridView. Dans ce didacticiel, nous avons examiné comment ajouter une colonne de cases d’option.

Malheureusement, l’ajout d’une colonne de case d’option boutons n’est pas simple ou simple que celle attendue. Il n’existe aucun RadioButtonField intégré qui peut être ajouté à un clic sur un bouton et l’utilisation du contrôle RadioButton Web dans TemplateField présente son propre ensemble de problèmes. Au final, pour fournir une telle interface nous avoir créer un personnalisé `DataControlField` classe ou à l’injection HTML approprié en TemplateField pendant la `RowCreated` événement.

Avoir exploré l’ajout d’une colonne de cases d’option, nous permettent d’activer notre attention sur l’ajout d’une colonne de cases à cocher. Avec une colonne de cases à cocher, un utilisateur peut sélectionner une ou plusieurs lignes de GridView et puis effectuer une opération sur toutes les lignes sélectionnées (par exemple, en sélectionnant un ensemble de messages électroniques à partir d’un client de messagerie électronique basé sur le web, puis en choisissant de supprimer tous les messages électroniques sélectionnés). Dans l’étape suivante du didacticiel, nous verrons comment ajouter une colonne de ce type.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été David Suru. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](inserting-a-new-record-from-the-gridview-s-footer-cs.md)
[Suivant](adding-a-gridview-column-of-checkboxes-vb.md)
