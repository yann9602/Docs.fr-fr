---
uid: web-forms/overview/data-access/editing-and-deleting-data-through-the-datalist/an-overview-of-editing-and-deleting-data-in-the-datalist-cs
title: "Une vue d’ensemble de la modification et suppression des données dans le contrôle DataList (c#) | Documents Microsoft"
author: rick-anderson
description: "Lorsque le contrôle DataList ne dispose pas intégrés de modification et suppression de fonctionnalités, dans ce didacticiel nous verrons comment créer un contrôle DataList qui prend en charge la modification et suppression o..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/30/2006
ms.topic: article
ms.assetid: c3b0c86e-fe98-41ee-b26f-ca38cddaa75e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-and-deleting-data-through-the-datalist/an-overview-of-editing-and-deleting-data-in-the-datalist-cs
msc.type: authoredcontent
ms.openlocfilehash: 8b3067c5a6bcf81a35f66d43886c9b116a0ef7d8
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="an-overview-of-editing-and-deleting-data-in-the-datalist-c"></a>Une vue d’ensemble de la modification et suppression des données dans le contrôle DataList (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_36_CS.exe) ou [télécharger le PDF](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/datatutorial36cs1.pdf)

> Lorsque le contrôle DataList ne dispose pas intégrés de modification et suppression de fonctionnalités, dans ce didacticiel nous verrons comment créer un contrôle DataList qui prend en charge la modification et suppression de ses données sous-jacentes.


## <a name="introduction"></a>Introduction

Dans le [une vue d’ensemble d’insertion, de mise à jour et de suppression des données](../editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-cs.md) didacticiel, nous avons examiné comment insérer, mettre à jour et supprimer des données à l’aide de l’architecture d’application, un ObjectDataSource et le GridView, DetailsView et FormView contrôles. ObjectDataSource et ces trois données Web, implémenter des interfaces de modification de données simple est un composant logiciel enfichable et impliqués simplement son cycle une case à cocher à partir d’une balise active. Aucun code n’est nécessaire pour être écrites.

Malheureusement, DataList ne dispose pas de modification et suppression des capacités dans le contrôle GridView intégrés. Cette fonctionnalité manquante est due en partie au fait que le contrôle DataList est un relic à partir de la version précédente d’ASP.NET, lorsque les contrôles de source de données déclarative et pages de modification de données sans code n’étaient pas disponibles. Alors que DataList dans ASP.NET 2.0 n’offre pas la même en dehors de la zone de modification des données en tant que le contrôle GridView, nous pouvons utiliser ASP.NET 1.x techniques pour inclure ce type de fonctionnalité. Cette approche nécessite un peu de code, mais comme nous allons le voir dans ce didacticiel, le contrôle DataList a certains événements et les propriétés en place à l’aide de ce processus.

Dans ce didacticiel, nous verrons comment créer un contrôle DataList qui prend en charge la modification et suppression de ses données sous-jacentes. Didacticiels futures examinera plus avancées de modification et suppression des scénarios, y compris la validation de champ d’entrée, normalement la gestion des exceptions levées à partir de l’accès aux données ou couches de logique métier et ainsi de suite.

> [!NOTE]
> Comme le contrôle DataList, le contrôle du répéteur ne dispose pas hors de la fonctionnalité de zone d’insertion, de mise à jour ou de suppression. Alors que ces fonctionnalités peuvent être ajoutées, DataList inclut les propriétés et événements introuvable dans le répéteur qui simplifient l’ajout de ces fonctionnalités. Par conséquent, ce didacticiel et futurs qui examinent la modification et suppression concentrerons strictement sur le contrôle DataList.


## <a name="step-1-creating-the-editing-and-deleting-tutorials-web-pages"></a>Étape 1 : Créer les Pages Web didacticiels modification et suppression

Avant d’aborder la manière de mettre à jour et supprimer des données à partir d’un contrôle DataList, permettent de s tout d’abord prendre quelques instants pour créer les pages ASP.NET dans notre projet de site Web que nous aurons besoin pour ce didacticiel et celles plusieurs suivants. Commencez par ajouter un nouveau dossier nommé `EditDeleteDataList`. Ensuite, ajoutez les pages ASP.NET suivantes à ce dossier, en veillant à associer chaque page avec le `Site.master` page maître :

- `Default.aspx`
- `Basics.aspx`
- `BatchUpdate.aspx`
- `ErrorHandling.aspx`
- `UIValidation.aspx`
- `CustomizedUI.aspx`
- `OptimisticConcurrency.aspx`
- `ConfirmationOnDelete.aspx`
- `UserLevelAccess.aspx`


![Ajouter les Pages ASP.NET pour les didacticiels](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image1.png)

**Figure 1**: ajouter les Pages ASP.NET pour les didacticiels


Comme dans les autres dossiers, `Default.aspx` dans le `EditDeleteDataList` dossier répertorie les didacticiels dans sa section. N’oubliez pas que le `SectionLevelTutorialListing.ascx` contrôle utilisateur fournit cette fonctionnalité. Par conséquent, ajoutez ce contrôle utilisateur pour `Default.aspx` en le faisant glisser à partir de l’Explorateur de solutions sur la page s mode Création.


[![Ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image3.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image2.png)

**Figure 2**: ajouter la `SectionLevelTutorialListing.ascx` contrôle utilisateur à `Default.aspx` ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image4.png))


Enfin, ajoutez les pages en tant qu’entrées pour les `Web.sitemap` fichier. En particulier, ajoutez le balisage suivant après les rapports maître/détail avec le contrôle DataList et répéteur `<siteMapNode>`:


[!code-xml[Main](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/samples/sample1.xml)]

Après la mise à jour `Web.sitemap`, prenez un moment pour afficher le site Web des didacticiels via un navigateur. Le menu de gauche inclut désormais les éléments de DataList, modification et suppression des didacticiels.


![Le plan de Site inclut désormais les entrées de DataList, modification et suppression des didacticiels](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image5.png)

**Figure 3**: le plan de Site inclut désormais les entrées de DataList, modification et suppression des didacticiels


## <a name="step-2-examining-techniques-for-updating-and-deleting-data"></a>Étape 2 : Examen des Techniques pour la mise à jour et suppression de données

Modification et suppression des données avec le contrôle GridView sont donc facile, car, dans les coulisses, GridView et ObjectDataSource fonctionnent de concert. Comme indiqué dans le [examiner les événements associés insertion, mise à jour et suppression](../editing-inserting-and-deleting-data/examining-the-events-associated-with-inserting-updating-and-deleting-cs.md) didacticiel, un clic sur un bouton de mise à jour de ligne s, le contrôle GridView attribue automatiquement ses champs servant de liaison de données bidirectionnelle pour la `UpdateParameters` collection de son ObjectDataSource puis appelle s ObjectDataSource `Update()` (méthode).

Malheureusement, le contrôle DataList ne fournit pas une de ces fonctionnalités intégrées. Il incombe notre pour vous assurer que les valeurs de l’utilisateur s sont affectées pour les paramètres de s ObjectDataSource et que son `Update()` méthode est appelée. Pour faciliter cette tâche, le contrôle DataList fournit les propriétés et les événements suivants :

- **Le [ `DataKeyField` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.basedatalist.datakeyfield.aspx)**  lors de la mise à jour ou la suppression, nous devons être en mesure d’identifier de façon unique chaque élément dans le contrôle DataList. Définissez cette propriété sur le champ de clé primaire des données affichées. Cela remplira du contrôle DataList s [ `DataKeys` collection](https://msdn.microsoft.com/library/system.web.ui.webcontrols.basedatalist.datakeys.aspx) avec l’objet `DataKeyField` pour chaque élément du contrôle DataList.
- **Le [ `EditCommand` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalist.editcommand.aspx)**  se déclenche lorsqu’un bouton, LinkButton ou ImageButton dont `CommandName` est définie sur un clic sur Modifier.
- **Le [ `CancelCommand` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalist.cancelcommand.aspx)**  se déclenche lorsqu’un bouton, LinkButton ou ImageButton dont `CommandName` est définie sur un clic sur Annuler.
- **Le [ `UpdateCommand` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalist.updatecommand.aspx)**  se déclenche lorsqu’un bouton, LinkButton ou ImageButton dont `CommandName` est définie sur mise à jour est activé.
- **Le [ `DeleteCommand` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalist.deletecommand.aspx)**  se déclenche lorsqu’un bouton, LinkButton ou ImageButton dont `CommandName` est définie sur un clic sur Supprimer.

À l’aide de ces propriétés et des événements, il existe quatre approches, que nous pouvons utiliser pour mettre à jour et supprimer des données à partir du contrôle DataList :

1. **À l’aide d’ASP.NET 1.x Techniques** DataList existait avant ASP.NET 2.0 et ObjectDataSources et qu’il a été en mesure de mettre à jour et supprimer des données entièrement par le biais de moyens par programme. Cette technique fossés ObjectDataSource complètement et requiert que nous lier les données du contrôle DataList directement à partir de la couche de logique métier, à la fois les données à afficher lors de l’extraction et la mise à jour ou suppression d’un enregistrement.
2. **À l’aide d’un seul contrôle ObjectDataSource sur la Page de sélection, de mise à jour et de suppression** lorsque le contrôle DataList ne dispose pas s inhérents à la modification de GridView et de suppression de capacités, s’il n’y a aucune raison que nous pouvons t n’ajoutez-les dans nous-mêmes. Avec cette approche, nous utilisent un ObjectDataSource comme dans les exemples de GridView, mais il doit créer un gestionnaire d’événements pour le contrôle DataList s `UpdateCommand` événement où nous avons défini les paramètres de s ObjectDataSource et appellent son `Update()` (méthode).
3. **À l’aide d’un contrôle ObjectDataSource pour la sélection, mais mise à jour et supprimer directement par rapport à la couche BLL** lorsque vous utilisez l’option 2, nous devons écrire du code dans le `UpdateCommand` événement, l’affectation de valeurs de paramètre et ainsi de suite. Au lieu de cela, nous pouvons coller à l’aide de ObjectDataSource pour la sélection, mais effectuer les appels de mise à jour et suppression directement sur la couche BLL (par exemple, avec option 1). À mon avis, mise à jour des données en créant une interface directement avec la couche BLL entraîne un code plus lisible que l’affectation de la s ObjectDataSource `UpdateParameters` et en appelant sa `Update()` (méthode).
4. **À l’aide d’un moyen déclaratif via plusieurs ObjectDataSources** précédentes trois approches tous les nécessitent un peu de code. Si vous d plutôt continuer à utiliser en tant que la syntaxe déclarative autant que possible, une option finale consiste à inclure plusieurs ObjectDataSources sur la page. La première ObjectDataSource extrait les données de la couche BLL et le lie à du contrôle DataList. Pour mettre à jour, un autre ObjectDataSource est ajouté, mais ajouté directement au sein de DataList s `EditItemTemplate`. Pour inclure la prise en charge de suppression, encore une autre ObjectDataSource est nécessaire dans le `ItemTemplate`. Avec cette approche, ces incorporé ObjectDataSource s utiliser `ControlParameters` pour lier de façon déclarative les paramètres de s ObjectDataSource aux contrôles d’entrée utilisateur (au lieu d’avoir à les spécifier par programme dans le contrôle DataList s `UpdateCommand` Gestionnaire d’événements). Cette approche nécessite toujours un peu de code que nous avons besoin d’appeler le s ObjectDataSource incorporé `Update()` ou `Delete()` commande mais nécessite beaucoup moins d’avec les autres trois approches. L’inconvénient est que les ObjectDataSources plusieurs encombrer la page, lui la lisibilité globale.

Si forcé à seulement jamais utiliser une de ces approches, d choisir l’option 1, car il fournit la plus grande souplesse et parce que le contrôle DataList a été conçue pour prendre en charge ce modèle. DataList a été étendue pour fonctionner avec les contrôles de source de données ASP.NET 2.0, mais il ne dispose pas tous les points d’extensibilité ou les fonctionnalités de données ASP.NET 2.0 officielles contrôles Web (le GridView, DetailsView et FormView). Options de 2 à 4 ne sont pas sans mérite, cependant.

Cela future modification et suppression des didacticiels utilisera un ObjectDataSource de récupération des données pour afficher et diriger les appels à la couche BLL à mettre à jour et supprimer des données (option 3).

## <a name="step-3-adding-the-datalist-and-configuring-its-objectdatasource"></a>Étape 3 : Ajout du contrôle DataList et configuration de son ObjectDataSource

Dans ce didacticiel, nous allons créer un contrôle DataList qui répertorie des informations de produit et, pour chaque produit, permet de l’utilisateur à modifier le nom et le prix et à supprimer du produit. En particulier, nous récupérer les enregistrements à l’aide d’ObjectDataSource, mais effectuer la mise à jour et supprimer des actions par interface directement avec la couche BLL. Avant de nous soucier d’implémenter les fonctionnalités de modification et de suppression pour le contrôle DataList, permettent de s tout d’abord obtenir la page pour afficher les produits dans une interface en lecture seule. Dans la mesure où ve examiné ces étapes dans les didacticiels précédents, vous allez passer via les rapidement.

Commencez par ouvrir le `Basics.aspx` page dans le `EditDeleteDataList` dossier et, dans la vue conception, ajoutez un contrôle DataList à la page. Ensuite, à partir de la balise active de s DataList, créez un nouveau ObjectDataSource. Étant donné que nous travaillons avec les données de produit, configurez-le pour utiliser le `ProductsBLL` classe. Pour récupérer *tous les* produits, choisissez la `GetProducts()` méthode dans l’onglet sélection.


[![Configurer pour utiliser la classe ProductsBLL ObjectDataSource](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image7.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image6.png)

**Figure 4**: configurer l’ObjectDataSource à utiliser le `ProductsBLL` classe ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image8.png))


[![Retourne les informations de produit à l’aide de la méthode GetProducts()](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image10.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image9.png)

**Figure 5**: renvoyer les informations de produit à l’aide de la `GetProducts()` (méthode) ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image11.png))


Le contrôle DataList, comme le GridView, n’est pas conçu pour l’insertion de nouvelles données ; Par conséquent, sélectionnez (aucun) option dans la liste déroulante sous l’onglet Insertion. Également (aucun) pour les onglets UPDATE et DELETE dans la mesure où les mises à jour et suppressions seront effectuées par programmation via la couche BLL.


[![Vérifier que les listes déroulantes dans le s ObjectDataSource insertion, mise à jour et supprimer des onglets sont définis à (None)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image13.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image12.png)

**Figure 6**: vérifier que les listes déroulantes dans le s insertion, mise à jour et supprimer des onglets ObjectDataSource sont définis à (None) ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image14.png))


Après avoir configuré l’ObjectDataSource, cliquez sur Terminer, retourner au concepteur. Comme nous avons vu dans ces exemples, lorsque la fin de la configuration de ObjectDataSource, Visual Studio automatiquement crée un `ItemTemplate` pour DropDownList, affichage de chacun des champs de données. Remplacez ceci `ItemTemplate` avec celui qui affiche uniquement le nom de produit s et le prix. En outre, définissez le `RepeatColumns` propriété à 2.

> [!NOTE]
> Comme indiqué dans le *vue d’ensemble de l’insertion, mise à jour et suppression des données* (didacticiel), lors de la modification des données à l’aide de ObjectDataSource notre architecture requiert que nous supprime le `OldValuesParameterFormatString` propriété à partir de la s ObjectDataSource balisage déclaratif (ou de le réinitialiser à sa valeur par défaut, `{0}`). Dans ce didacticiel, toutefois, nous utilisons ObjectDataSource uniquement pour la récupération des données. Par conséquent, nous n’avez pas besoin de modifier le s ObjectDataSource `OldValuesParameterFormatString` valeur de la propriété (bien qu’il ne conseillé de le faire).


Après le remplacement de la valeur par défaut du contrôle DataList `ItemTemplate` avec une, le balisage déclaratif sur votre page doit ressembler à ce qui suit :


[!code-aspx[Main](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/samples/sample2.aspx)]

Prenez un moment pour afficher la progression via un navigateur. Comme le montre la Figure 7, DataList affiche le prix du produit nom et une unité pour chaque produit dans deux colonnes.


[![Les noms de produits et les prix sont affichés dans un contrôle DataList deux colonnes](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image16.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image15.png)

**Figure 7**: les noms de produits et les prix sont affichés dans un contrôle DataList deux colonnes ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image17.png))


> [!NOTE]
> DataList a un nombre de propriétés qui sont requis pour le processus de mise à jour et suppression, et ces valeurs sont stockées dans l’état d’affichage. Par conséquent, lors de la création d’un contrôle DataList qui prend en charge la modification ou la suppression des données, il est essentiel que l’état d’affichage DataList s soit activé.  
>   
>  Le lecteur perspicace nous l’avons vu que nous avons pu désactiver l’état d’affichage lors de la création de contrôles GridView, DetailsViews et FormViews modifiable. Il s’agit, car les contrôles Web ASP.NET 2.0 peuvent inclure *état du contrôle*, qui est état persistant entre publications (postback) comme état d’affichage, mais indispensables présumé.


Désactiver l’affichage de l’état dans le GridView simplement omet les informations d’état triviale, mais conserve l’état du contrôle (qui inclut l’état nécessaire pour la modification et suppression). Le contrôle DataList ayant été créé dans le délai d’exécution ASP.NET 1.x, n’utilise pas l’état du contrôle et doit donc état d’affichage. Consultez [vs de l’état du contrôle. État d’affichage](https://msdn.microsoft.com/library/1whwt1k7.aspx) pour plus d’informations sur l’objectif de l’état du contrôle et comment il diffère de l’état d’affichage.

## <a name="step-4-adding-an-editing-user-interface"></a>Étape 4 : Ajout d’une Interface utilisateur de modification

Le contrôle GridView est composé d’une collection de champs (BoundFields, CheckBoxFields, TemplateField et ainsi de suite). Ces champs peuvent ajuster leur balisage rendu en fonction de leur mode de. Par exemple, en mode lecture seule, un BoundField affiche sa valeur de champ de données sous forme de texte ; en mode édition, elle restitue une zone de texte de Web contrôle dont `Text` est affectée à la valeur de champ de données.

Le contrôle DataList restitue quant à eux, ses éléments à l’aide de modèles. Éléments en lecture seule sont rendus à l’aide de la `ItemTemplate` tandis que les éléments en mode d’édition sont rendus la `EditItemTemplate`. À ce stade, notre DataList a uniquement un `ItemTemplate`. Pour prendre en charge la fonctionnalité de modification au niveau élément nous devons ajouter une `EditItemTemplate` qui contient le balisage à afficher pour l’élément modifiable. Pour ce didacticiel, nous allons utiliser des contrôles Web de la zone de texte pour modifier le produit s nom et le prix unitaire.

Le `EditItemTemplate` peuvent être créés ou via le Concepteur de façon déclarative (en sélectionnant l’option Modifier les modèles à partir de la balise active du contrôle DataList s). Pour utiliser l’option Modifier les modèles, cliquez d’abord sur le lien Modifier les modèles dans la balise active, puis le `EditItemTemplate` élément dans la liste déroulante.


[![Opter pour une utilisation avec le modèle EditItemTemplate s de DataList](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image19.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image18.png)

**Figure 8**: opter pour une utilisation avec le contrôle DataList s `EditItemTemplate` ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image20.png))


Ensuite, tapez dans nom du produit : et le prix : puis faites glisser deux contrôles de zone de texte de la boîte à outils dans le `EditItemTemplate` interface sur le concepteur. Définir les zones de texte `ID` propriétés `ProductName` et `UnitPrice`.


[![Ajouter une zone de texte pour le nom de produit et le prix](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image22.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image21.png)

**Figure 9**: ajouter une zone de texte pour le nom de produit et le prix ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image23.png))


Nous avons besoin lier les valeurs de champ de données produit correspondant à la `Text` propriétés des deux zones de texte. Dans les balises actives de zones de texte, cliquez sur le lien Modifier les DataBindings, puis associez le champ de données approprié avec le `Text` propriété, comme indiqué dans la Figure 10.

> [!NOTE]
> Lors de la liaison la `UnitPrice` champ de données pour le prix de zone de texte s `Text` champ, vous pouvez formater en tant que valeur monétaire (`{0:C}`), un nombre Général (`{0:N}`), ou laissez-le sans mise en forme.


![Lier les champs de données de UnitPrice ProductName pour les propriétés du texte des zones de texte](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image24.png)

**La figure 10**: lier le `ProductName` et `UnitPrice` des champs de données pour les `Text` propriétés des zones de texte


Notez le fait de la boîte de dialogue Modifier les DataBindings dans la Figure 10 *pas* incluent la case à cocher de liaison de données bidirectionnelle est présent lors de la modification dans le contrôle GridView ou DetailsView TemplateField, ou un modèle dans le FormView. La fonctionnalité de liaison de données bidirectionnelle autorisée de la valeur entrée dans le contrôle Web d’entrée soit attribué automatiquement au s ObjectDataSource correspondant `InsertParameters` ou `UpdateParameters` lors de l’insertion ou de mise à jour des données. DataList ne prend pas en charge la liaison de données bidirectionnelle que nous verrons plus tard dans ce didacticiel, une fois l’utilisateur effectue son travail change et est prêt à mettre à jour les données, nous devons accéder par programmation à ces zones de texte `Text` propriétés et leurs valeurs à passer le approprié `UpdateProduct` méthode dans la `ProductsBLL` classe.

Enfin, nous devons ajouter la mise à jour et annuler des boutons à la `EditItemTemplate`. Comme nous l’avons vu dans la [maître/détail à l’aide d’une liste à puces des enregistrements principaux avec détails DataList](../filtering-scenarios-with-the-datalist-and-repeater/master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-cs.md) (didacticiel), lorsqu’un bouton, LinkButton ou ImageButton dont `CommandName` est définie est activé à partir de dans un contrôle DataList, ou un répéteur le S répéteur ou DataList `ItemCommand` événement est déclenché. Pour le contrôle DataList si la `CommandName` est définie sur une certaine valeur, un événement supplémentaire peut ainsi être déclenché. Spéciale `CommandName` valeurs de propriété incluent, entre autres :

- Annuler déclenche le `CancelCommand` événement
- Modifier déclenche le `EditCommand` événement
- Mettre à jour déclenche le `UpdateCommand` événement

N’oubliez pas que ces événements sont déclenchés *à* le `ItemCommand` événement.

Ajouter à la `EditItemTemplate` deux contrôles bouton, un dont `CommandName` est défini sur la mise à jour et les autres s défini sur Annuler. Après l’ajout de ces deux contrôles bouton Web le concepteur doit ressembler à ce qui suit :


[![Ajouter la mise à jour et annuler des boutons à EditItemTemplate](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image26.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image25.png)

**Figure 11**: ajouter la mise à jour et les boutons Annuler le `EditItemTemplate` ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image27.png))


Avec le `EditItemTemplate` terminé votre balisage déclaratif DataList s doit ressembler à ce qui suit :


[!code-aspx[Main](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/samples/sample3.aspx)]

## <a name="step-5-adding-the-plumbing-to-enter-edit-mode"></a>Étape 5 : Ajout de l’élément pour entrer en Mode Édition

À ce stade notre DataList a une interface de modification définie via son `EditItemTemplate`; Toutefois, cet emplacement s actuellement aucun moyen pour un utilisateur accédant à notre page pour indiquer qu’il souhaite modifier une information produit s. Nous devons ajouter un bouton Modifier pour chaque produit que, lorsque vous cliquez dessus, restitue que DataList d’élément en mode édition. Commencez par ajouter un bouton Modifier pour le `ItemTemplate`, soit par le biais du concepteur ou de façon déclarative. Veillez à définir le bouton Modifier s `CommandName` propriété à modifier.

Après avoir ajouté ce bouton Modifier, prenez un moment pour afficher la page via un navigateur. Cet ajout, chaque liste de produits doit inclure un bouton Modifier.


[![Ajouter la mise à jour et annuler des boutons à EditItemTemplate](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image29.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image28.png)

**Figure 12**: ajouter la mise à jour et les boutons Annuler le `EditItemTemplate` ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image30.png))


Cliquez sur le bouton provoque une publication (postback), mais *pas* mettre le produit dans le mode édition. Pour modifier le produit, nous devons :

1. Valeur du contrôle DataList s [ `EditItemIndex` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalist.edititemindex.aspx) à l’index de la `DataListItem` dont modifier simplement bouton.
2. Lier de nouveau les données du contrôle DataList. Lorsque le contrôle DataList est restituée, la `DataListItem` dont `ItemIndex` correspond à du contrôle DataList s `EditItemIndex` sera rendu à l’aide de son `EditItemTemplate`.

Depuis le contrôle DataList s `EditCommand` événement est déclenché lorsque l’utilisateur clique sur le bouton Modifier, créer un `EditCommand` Gestionnaire d’événements avec le code suivant :


[!code-csharp[Main](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/samples/sample4.cs)]

Le `EditCommand` Gestionnaire d’événements est passé dans un objet de type `DataListCommandEventArgs` comme deuxième paramètre d’entrée, qui inclut une référence à la `DataListItem` dont bouton Modifier (`e.Item`). Le Gestionnaire d’événements définit tout d’abord du contrôle DataList s `EditItemIndex` à la `ItemIndex` de la liste modifiable `DataListItem` , puis relie les données au contrôle DataList en appelant du contrôle DataList s `DataBind()` (méthode).

Après avoir ajouté ce gestionnaire d’événements, modifier la page dans un navigateur. En cliquant sur le bouton Modifier maintenant rend le produit où vous avez cliqué modifiable (voir Figure 13).


[![En cliquant sur le fait de bouton Modifier le produit modifiable](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image32.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image31.png)

**Figure 13**: en cliquant sur le bouton Modifier permet du modifier de produit ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image33.png))


## <a name="step-6-saving-the-user-s-changes"></a>Étape 6 : L’enregistrement des modifications s utilisateur

En cliquant sur le produit modifié s mise à jour ou les boutons Annuler n’exécute aucune opération à ce stade ; Pour ajouter cette fonctionnalité, nous devons créer des gestionnaires d’événements pour le contrôle DataList s `UpdateCommand` et `CancelCommand` les événements. Commencez par créer le `CancelCommand` Gestionnaire d’événements qui s’exécute lorsque le bouton Annuler produit modifié s et il chargé de restaurer l’état préalable édition du contrôle DataList.

Pour que le contrôle DataList restituer tous ses éléments dans le mode lecture seule, nous devons :

1. Valeur du contrôle DataList s [ `EditItemIndex` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalist.edititemindex.aspx) à l’index d’un inexistant `DataListItem` index. `-1`est un choix sûr, étant donné que la `DataListItem` index commencent à `0`.
2. Lier de nouveau les données du contrôle DataList. Etant donné que `DataListItem` `ItemIndex` es correspondent à du contrôle DataList s `EditItemIndex`, DataList entière sera rendue dans un mode en lecture seule.

Ces étapes peuvent être accomplies avec le code de gestionnaire d’événements suivantes :


[!code-csharp[Main](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/samples/sample5.cs)]

Avec cet ajout, en cliquant sur la retourne du bouton Annuler du contrôle DataList à son état avant modification.

Le dernier gestionnaire d’événements nécessaires est le `UpdateCommand` Gestionnaire d’événements. Ce gestionnaire d’événements doit :

1. Accès par programme le nom de l’utilisateur a entré un produit et prix, ainsi que le produit modifié s `ProductID`.
2. Lancer le processus de mise à jour en appelant approprié `UpdateProduct` surcharge dans les `ProductsBLL` classe.
3. Valeur du contrôle DataList s [ `EditItemIndex` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalist.edititemindex.aspx) à l’index d’un inexistant `DataListItem` index. `-1`est un choix sûr, étant donné que la `DataListItem` index commencent à `0`.
4. Lier de nouveau les données du contrôle DataList. Etant donné que `DataListItem` `ItemIndex` es correspondent à du contrôle DataList s `EditItemIndex`, DataList entière sera rendue dans un mode en lecture seule.

Les étapes 1 et 2 sont chargés pour enregistrer les modifications de s ; de l’utilisateur les étapes 3 et 4 retournent du contrôle DataList à son état avant modification une fois que les modifications ont été enregistrées et sont identiques aux étapes effectuées dans le `CancelCommand` Gestionnaire d’événements.

Pour obtenir le nom du produit mis à jour et le prix, nous devons utiliser la `FindControl` méthode par programmation la référence Web de la zone de texte des contrôles dans le `EditItemTemplate`. Nous devons également obtenir le produit modifié s `ProductID` valeur. Lorsque nous initialement lié ObjectDataSource au contrôle DataList, Visual Studio affectée du contrôle DataList s `DataKeyField` propriété la valeur de clé primaire à partir de la source de données (`ProductID`). Cette valeur peut ensuite être récupérée à partir du contrôle DataList s `DataKeys` collection. Prenez un moment pour vous assurer que le `DataKeyField` est bien définie sur `ProductID`.

Le code suivant implémente les quatre étapes :


[!code-csharp[Main](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/samples/sample6.cs)]

Le Gestionnaire d’événements démarre en lisant le produit modifié s `ProductID` à partir de la `DataKeys` collection. Ensuite, les deux zones de texte dans le `EditItemTemplate` référencés et leurs `Text` propriétés stockées dans les variables locales, `productNameValue` et `unitPriceValue`. Nous utilisons le `Decimal.Parse()` méthode pour lire la valeur de la `UnitPrice` zone de texte afin que se la valeur entrée est un symbole de devise, il peut toujours être correctement converti en un `Decimal` valeur.

> [!NOTE]
> Les valeurs à partir de la `ProductName` et `UnitPrice` zones de texte sont uniquement affectées aux variables productNameValue et unitPriceValue si les propriétés du texte des zones de texte ont une valeur spécifiée. Sinon, une valeur de `Nothing` est utilisé pour les variables, ce qui a pour effet de la mise à jour des données avec une base de données `NULL` valeur. Autrement dit, notre code traite convertit les chaînes pour la base de données vides `NULL` valeurs, qui est le comportement par défaut de l’interface de modification dans les contrôles GridView, DetailsView et FormView.


Après avoir lu les valeurs, le `ProductsBLL` classe s `UpdateProduct` est appelée, en passant le nom de produit s, prix, et `ProductID`. Le Gestionnaire d’événements se termine par retour du contrôle DataList à son état avant modification à l’aide de la même logique exacte que dans le `CancelCommand` Gestionnaire d’événements.

Avec la `EditCommand`, `CancelCommand`, et `UpdateCommand` terminer des gestionnaires d’événements, un visiteur peut modifier le nom et le prix d’un produit. Figures 14 à 16 afficher ce flux de travail dans l’action.


[![Lorsque la première visite de la Page, tous les produits sont en Mode lecture seule](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image35.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image34.png)

**La figure 14**: lors de la première visite de la Page, tous les produits sont en Mode lecture seule ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image36.png))


[![Pour mettre à jour un produit s nom ou son prix, cliquez sur le bouton Modifier](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image38.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image37.png)

**Figure 15**: pour mettre à jour d’un nom de produit ou le prix, cliquez sur le bouton Modifier ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image39.png))


[![Après avoir modifié la valeur, cliquez sur la mise à jour pour revenir à la lecture seule](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image41.png)](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image40.png)

**Figure 16**: après la modification de la valeur, cliquez sur la mise à jour à retourner pour le Mode en lecture seule ([cliquez pour afficher l’image en taille réelle](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/_static/image42.png))


## <a name="step-7-adding-delete-capabilities"></a>Étape 7 : Ajout de fonctionnalités de suppression

Les étapes de l’ajout de fonctionnalités de suppression à un contrôle DataList sont semblables à celles permettant d’ajouter des fonctionnalités d’édition. En résumé, nous devons ajouter un bouton Supprimer pour le `ItemTemplate` qui, quand vous cliquez dessus :

1. Lit dans le produit correspondant s `ProductID` via la `DataKeys` collection.
2. Exécute la suppression en appelant le `ProductsBLL` classe s `DeleteProduct` (méthode).
3. Relie les données du contrôle DataList.

S permettent de commencer par ajouter un bouton Supprimer pour le `ItemTemplate`.

Quand vous cliquez dessus, un bouton dont `CommandName` est mise à jour, modifier ou Annuler déclenche du contrôle DataList s `ItemCommand` événement ainsi que d’un événement supplémentaire (par exemple, lors de l’utilisation de modifier le `EditCommand` événement est également déclenché). De même, n’importe quel bouton, LinkButton ou ImageButton dans le contrôle DataList dont `CommandName` est définie sur les causes de supprimer le `DeleteCommand` l’événement (avec `ItemCommand`).

Ajouter un bouton Supprimer en regard du bouton Modifier dans le `ItemTemplate`, ce qui affecte ses `CommandName` propriété à supprimer. Après avoir ajouté ce contrôle bouton votre DataList s `ItemTemplate` la syntaxe déclarative doit ressembler à :


[!code-aspx[Main](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/samples/sample7.aspx)]

Ensuite, créez un gestionnaire d’événements pour le contrôle DataList s `DeleteCommand` événement, en utilisant le code suivant :


[!code-csharp[Main](an-overview-of-editing-and-deleting-data-in-the-datalist-cs/samples/sample8.cs)]

En cliquant sur le bouton Supprimer provoque une publication (postback) et se déclenche du contrôle DataList s `DeleteCommand` événement. Dans le Gestionnaire d’événements, le produit d’un clic sur s `ProductID` valeur est accessible à partir du `DataKeys` collection. Ensuite, le produit est supprimé en appelant le `ProductsBLL` classe s `DeleteProduct` (méthode).

Après la suppression du produit, elle s importants que nous lier de nouveau les données du contrôle DataList (`DataList1.DataBind()`), sinon DataList continue d’afficher le produit qui a été supprimé uniquement.

## <a name="summary"></a>Récapitulatif

DataList ne possède pas le point et sur la modification et suppression de prise en charge de GridView, avec un court peu de code il peut être améliorée pour inclure ces fonctionnalités. Dans ce didacticiel, nous avons vu comment créer une liste de deux colonnes de produits qui pourrait être supprimée et dont le nom et prix a pu être modifiés. Ajout de modification et suppression de prise en charge consiste à y compris les contrôles Web appropriés dans le `ItemTemplate` et `EditItemTemplate`, créer les gestionnaires d’événements correspondants, lire les valeurs de clé primaires et entré par l’utilisateur et interagissant avec l’entreprise Couche de logique.

Pendant que nous avons ajouté les modifications de base et la suppression des fonctionnalités du contrôle DataList, il ne dispose pas de fonctionnalités plus avancées. Par exemple, il n’existe aucune validation de champ d’entrée - si un utilisateur entre un prix de trop coûteuse, une exception est levée par `Decimal.Parse` lors de la tentative de conversion trop coûteuses dans un `Decimal`. De même, s’il existe un problème de mise à jour les données au niveau de la logique métier ou des couches d’accès aux données à l’utilisateur s’affiche avec l’écran d’erreur standard. Sans risque de confirmation sur le bouton Supprimer, suppression accidentelle d’un produit est trop probable.

Dans les futures rencontrer des didacticiels, nous verrons comment améliorer l’édition utilisateur.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont Zack Jones, Ken Pespisa et Randy Schmidt. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Next](performing-batch-updates-cs.md)
