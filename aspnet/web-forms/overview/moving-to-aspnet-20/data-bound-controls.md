---
uid: web-forms/overview/moving-to-aspnet-20/data-bound-controls
title: "Contrôles liés aux données | Documents Microsoft"
author: microsoft
description: "La plupart des applications ASP.NET s’appuient sur une certaine de présentation des données à partir d’une source de données back-end. Contrôles liés aux données ont été une partie essentielle de w interaction..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2005
ms.topic: article
ms.assetid: 0e23ff32-646d-43f3-8bec-6b2313d3abd6
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/moving-to-aspnet-20/data-bound-controls
msc.type: authoredcontent
ms.openlocfilehash: 3ebb0f9a7a2f071b7bf7aa3855920f1a5784a61f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="data-bound-controls"></a>Contrôles liés aux données
====================
par [Microsoft](https://github.com/microsoft)

> La plupart des applications ASP.NET s’appuient sur une certaine de présentation des données à partir d’une source de données back-end. Contrôles liés aux données ont été une partie essentielle de l’interaction avec les données dans les applications Web dynamiques. ASP.NET 2.0 introduit des améliorations significatives en termes de contrôles liés aux données, notamment une nouvelle classe BaseDataBoundControl et la syntaxe déclarative.


La plupart des applications ASP.NET s’appuient sur une certaine de présentation des données à partir d’une source de données back-end. Contrôles liés aux données ont été une partie essentielle de l’interaction avec les données dans les applications Web dynamiques. ASP.NET 2.0 introduit des améliorations significatives en termes de contrôles liés aux données, notamment une nouvelle classe BaseDataBoundControl et la syntaxe déclarative.

Le BaseDataBoundControl agit comme classe de base pour la classe DataBoundControl et la classe HierarchicalDataBoundControl. Dans ce module, nous allons décrire les classes suivantes qui dérivent de DataBoundControl :

- AdRotator
- Contrôles de liste
- GridView
- FormView
- Contrôle DetailsView

Nous aborderons également les classes suivantes qui dérivent de la classe de HierarchicalDataBoundControl :

- TreeView
- Menu
- SiteMapPath

## <a name="databoundcontrol-class"></a>Classe de DataBoundControl

La classe DataBoundControl est une classe abstraite (marquée MustInherit dans VB) utilisée pour interagir avec le format tabulaire ou données de style de la liste. Les contrôles suivants sont des contrôles qui dérivent de DataBoundControl.

## <a name="adrotator"></a>AdRotator

Le contrôle AdRotator permet d’afficher une bannière graphique sur une page Web qui est liée à une URL spécifique. Le graphique est affiché est pivoté à l’aide des propriétés pour le contrôle. La fréquence d’affichage d’une annonce sur une page peut être configurée à l’aide de la **Impressions** propriété et des publicités peuvent être filtrées à l’aide du filtrage de mots clés.

Contrôles AdRotator utilisent un fichier XML ou une table dans une base de données pour les données. Les attributs suivants sont utilisés dans des fichiers XML pour configurer le contrôle AdRotator.

### <a name="imageurl"></a>ImageUrl
L’URL d’une image à afficher pour la publicité.

### <a name="navigateurl"></a>NavigateUrl
URL vers laquelle l’utilisateur devra être pris pour la suite d’un clic sur la publicité. Cela doit être codée URL.

### <a name="alternatetext"></a>AlternateText
Le texte de remplacement qui s’affiche dans une info-bulle et est lu par les lecteurs d’écran. Affiche également lorsque l’image spécifiée par la propriété ImageUrl n’est pas disponible.

### <a name="keyword"></a>Mot clé
Définit un mot clé qui peut être utilisé lors de l’utilisation du filtrage de mot clé. Si spécifié, seuls ces publicités avec un mot clé correspondant au filtre de mot clé seront affichera.

### <a name="impressions"></a>Impressions
Nombre de pondération qui détermine la fréquence à laquelle une publicité est susceptible d’apparaître. Il est relatif à l’impression d’autres annonces dans le même fichier. La valeur maximale des impressions collectives de toutes les annonces dans un fichier XML est 2,048,000,000 1.

### <a name="height"></a>Hauteur 
La hauteur de la publicité en pixels.

### <a name="width"></a>Largeur
La largeur de la publicité en pixels.


> [!NOTE]
> Les attributs de largeur et la hauteur substituent la hauteur et la largeur du contrôle AdRotator lui-même.


Un fichier XML classique peut se présenter comme suit :

[!code-xml[Main](data-bound-controls/samples/sample1.xml)]

Dans l’exemple ci-dessus, la publicité pour Contoso est deux fois plus que susceptible d’apparaître en tant qu’Active Directory pour le site Web ASP.NET en raison de la valeur de l’attribut Impressions.

Pour afficher des publicités à partir du fichier XML ci-dessus, ajouter un contrôle AdRotator à une page et définissez la **AdvertisementFile** propriété pour pointer vers le fichier XML, comme indiqué ci-dessous :

[!code-aspx[Main](data-bound-controls/samples/sample2.aspx)]

Si vous choisissez d’utiliser une table de base de données comme source de données pour votre contrôle AdRotator, vous devez d’abord configurer une base de données en utilisant le schéma suivant :

| **Nom de la colonne** | **Type de données** | **Description** |
| --- | --- | --- |
| Id | int | Clé primaire. Cette colonne peut avoir n’importe quel nom. |
| ImageUrl | nvarchar (*longueur*) | URL relative ou absolue de l’image à afficher pour la publicité. |
| NavigateUrl | nvarchar (*longueur*) | L’URL cible pour la publicité. Si vous ne fournissez pas de valeur, Active Directory n’est pas un lien hypertexte. |
| AlternateText | nvarchar (*longueur*) | Texte affiché si l’image ne peut pas être trouvée. Dans certains navigateurs, le texte est affiché en tant qu’une info-bulle. Texte de remplacement est également utilisé pour l’accessibilité afin que les utilisateurs qui ne peuvent pas consulter le graphique peuvent écouter la lecture de sa description. |
| Mot clé | nvarchar (*longueur*) | Une catégorie de la publicité sur lequel pouvez filtrer la page. |
| Impressions | int (4) | Nombre qui indique la probabilité de la fréquence à laquelle la publicité est affichée. Le plus grand nombre, le plus souvent la publicité doit être affichée. Le total de toutes les valeurs dans le fichier XML ne peut pas dépasser 2 048 000 000-1. |
| Largeur | int (4) | La largeur de l’image en pixels. |
| Hauteur  | int (4) | La hauteur de l’image en pixels. |

Dans les cas où vous disposez déjà d’une base de données avec un schéma différent, vous pouvez utiliser la **AlternateTextField**, **ImageUrlField**, et **NavigateUrlField** propriétés pour mapper les AdRotator des attributs à votre base de données existante. Pour afficher les données à partir de la base de données dans le contrôle AdRotator, ajoutez un contrôle de source de données à la page, configurer la chaîne de connexion pour le contrôle de source de données pointer vers votre base de données et définir le contrôle de AdRotator **DataSourceID** propriété de l’ID du contrôle de source de données. Dans les cas où vous avez besoin pour configurer les publicités AdRotator par programmation, utilisez l’événement AdCreated. L’événement AdCreated prend deux paramètres ; un objet et l’autre instance de AdCreatedEventArgs. Le AdCreatedEventArgs est une référence à la publicité est en cours de création.

L’extrait de code suivant définit la propriété ImageUrl, NavigateUrl et AlternateText pour une annonce par programme :

[!code-csharp[Main](data-bound-controls/samples/sample3.cs)]

## <a name="list-controls"></a>Contrôles de liste

Contrôles de liste incluent la zone de liste DropDownList, liste case à cocher, RadioButtonList et BulletedList. Chacune de ces contrôles peut être lié aux données d’une source de données. Ils utilisent un champ dans la source de données en tant que texte d’affichage et que vous peuvent éventuellement utiliser un deuxième champ comme valeur d’un élément. Les éléments peuvent également être ajoutés statiquement au moment du design, et vous pouvez associer des éléments statiques et dynamiques ajoutés à partir d’une source de données.

Pour les données lier un contrôle de liste, ajouter un contrôle de source de données à la page. Spécifiez une commande SELECT pour le contrôle de source de données, puis définissez la propriété DataSourceID du contrôle de liste à l’ID du contrôle de source de données. Utilisez le **DataTextField** et **DataValueField** propriétés pour définir le texte d’affichage et la valeur pour le contrôle. En outre, vous pouvez utiliser la **DataTextFormatString** propriété pour contrôler l’apparence du texte d’affichage comme suit :

| **Expression** | **Description** |
| --- | --- |
| Prix : {0:C} | Pour les données numériques/décimales. Affiche le littéral « prix : » suivie de nombres au format monétaire. Le format monétaire dépend du paramètre de culture spécifié dans l’attribut de culture sur le **Page** directive ou dans le fichier Web.config. |
| {0:D4} | Pour les données de type entier. Ne peut pas être utilisé avec des nombres décimaux. Les entiers sont affichés dans un champ de zéros de quatre caractères larges. |
| {0:N2} % | Pour les données numériques. Affiche le nombre avec 2 décimales précision suivie par le littéral « % ». |
| {0:000.0} | Pour les données numériques/décimales. Nombres sont arrondis à une décimale. Les nombres de moins de trois chiffres sont remplis de zéros. |
| {0:D} | Pour les données de date/heure. Format de date longue affiche (« Jeudi 06 août 1996 »). Le format de date dépend des paramètres de culture de la page ou du fichier Web.config. |
| {0:d} | Pour les données de date/heure. Mettre en forme affiche la date courte (« 31/12/99 »). |
| {0:yy-MM-dd} | Pour les données de date/heure. Affiche la date de format numérique année-mois-jour (96-08-06) |

## <a name="gridview"></a>GridView

Le contrôle GridView permet pour l’affichage de données tabulaires et de la modification à l’aide d’une approche déclarative et est le successeur du contrôle DataGrid. Les fonctionnalités suivantes sont disponibles dans le contrôle GridView.

- Liaison aux données des contrôles de source, tels que SqlDataSource.
- Fonctionnalités intégrées de tri.
- Intégré à jour et suppression de capacités.
- Fonctionnalités intégrées de pagination.
- Fonctionnalités de sélection de ligne intégré.
- L’accès par programme au modèle objet GridView pour définir dynamiquement les propriétés, gérer des événements et ainsi de suite.
- Plusieurs champs de clé.
- Plusieurs champs de données pour les colonnes de lien hypertexte.
- Apparence personnalisable grâce aux thèmes et styles.

**Champs de colonne**

Chaque colonne dans le contrôle GridView est représenté par un objet DataControlField. Par défaut, la propriété AutoGenerateColumns a la valeur **true**, ce qui crée un objet AutoGeneratedField pour chaque champ dans la source de données. Chaque champ est ensuite rendu en tant que colonne dans le contrôle GridView dans l’ordre dans lequel chaque champ s’affiche dans la source de données. Vous pouvez également contrôler les colonnes de champs s’affichent dans le **GridView** contrôle en définissant le **AutoGenerateColumns** propriété **false** et définir vos propres collection de champs de colonne. Types de champs de colonne différente déterminent le comportement des colonnes dans le contrôle.

Le tableau suivant répertorie les types de champs de colonne différente qui peuvent être utilisés.

| **Type de champ de colonne** | **Description** |
| --- | --- |
| BoundField | Affiche la valeur d’un champ dans une source de données. Il s’agit du type de colonne par défaut du contrôle GridView. |
| ButtonField | Affiche un bouton de commande pour chaque élément dans le contrôle GridView. Cela vous permet de créer une colonne de contrôles boutons personnalisés, tels que l’ajout ou le bouton Supprimer. |
| CheckBoxField | Affiche une case à cocher pour chaque élément dans le contrôle GridView. Ce type de champ de colonne est couramment utilisé pour afficher les champs avec une valeur booléenne. |
| CommandField | Affiche des prédéfinis des boutons de commande pour effectuer une sélection, la modification ou la suppression d’opérations. |
| HyperLinkField | Affiche la valeur d’un champ dans une source de données sous la forme d’un lien hypertexte. Ce type de champ de colonne vous permet de lier un deuxième champ vers les URL du lien hypertexte. |
| ImageField | Affiche une image pour chaque élément dans le contrôle GridView. |
| TemplateField | Affiche le contenu défini par l’utilisateur pour chaque élément dans le contrôle GridView selon un modèle spécifié. Ce type de champ de colonne vous permet de créer un champ de colonne personnalisé. |

Pour définir une collection de champs de colonne de façon déclarative, ajoutez d’abord fermer  **&lt;colonnes&gt;**  balises situées entre les balises d’ouverture et de fermeture du contrôle GridView. Ensuite, répertoriez les champs de colonne que vous souhaitez inclure entre les balises  **&lt;colonnes&gt;**  balises. Les colonnes spécifiées sont ajoutées à la collection de colonnes dans l’ordre indiqué. Le **colonnes** collection stocke tous les champs dans le contrôle de la colonne et vous permet de gérer par programme les champs de colonne dans le contrôle GridView.

Les champs de colonnes déclarés explicitement peuvent être affichés en combinaison avec les champs de colonne générée automatiquement. Lorsque les deux sont utilisés, les champs de colonnes déclarés explicitement sont rendus en premier, suivis des champs de colonne générée automatiquement.

## <a name="binding-to-data"></a>Liaison de données

Le contrôle GridView peut être lié à un contrôle de source de données (tel que **SqlDataSource**, **ObjectDataSource**, et ainsi de suite), ainsi que toute source de données qui implémente le pas ' System.Collections.IEnumerable ' interface (par exemple, System.Data.DataView, System.Collections.ArrayList ou System.Collections.Hashtable). Pour lier le contrôle GridView pour le type de source de données approprié, utilisez une des méthodes suivantes :

- Pour lier à un contrôle de source de données, définissez la propriété DataSourceID du contrôle GridView à la valeur d’ID du contrôle de source de données. Le contrôle GridView est lié au contrôle de source de données spécifié et peut tirer parti de la source de données des fonctionnalités de contrôle pour effectuer le tri, la mise à jour, suppression et la fonctionnalité de pagination automatiquement. Il s’agit de la méthode recommandée pour lier aux données.
- Pour lier à une source de données qui implémente l’interface System.Collections.IEnumerable, définir par programme la propriété DataSource du contrôle GridView à la source de données, puis appelez la méthode DataBind. Lorsque vous utilisez cette méthode, le contrôle GridView ne fournit pas intégrés tri, la mise à jour, suppression et d’échange. Vous devez fournir cette fonctionnalité vous-même.

## <a name="data-operations"></a>Opérations de données

Le contrôle GridView fournit plusieurs fonctionnalités intégrées qui permettent à l’utilisateur trier, mettre à jour, supprimer, sélectionnez et parcourir les éléments dans le contrôle. Lorsque le contrôle GridView est lié à un contrôle de source de données, le contrôle GridView peut tirer parti de la source de données de fonctionnalités du contrôle et fournir le tri automatique, la mise à jour et suppression de fonctionnalités.

> [!NOTE]
> Le contrôle GridView peut fournir la prise en charge de tri, de mise à jour et de suppression avec d’autres types de sources de données ; Toutefois, vous devez fournir un gestionnaire d’événements approprié avec l’implémentation de ces opérations.


Tri permet à l’utilisateur de trier les éléments dans le contrôle GridView par rapport à une colonne spécifique en cliquant sur l’en-tête de la colonne. Pour activer le tri, affectez à la propriété de AllowSorting **true**.

Les fonctionnalités de mise à jour, la suppression et la sélection automatique sont activées lorsqu’un bouton dans un **ButtonField** ou **TemplateField** champ de colonne, avec un nom de commande de « Modifier », « Delete » et « Select », respectivement, l’utilisateur clique. Le contrôle GridView peut ajouter automatiquement un **CommandField** champ de colonne avec un modifier, supprimer ou bouton de sélection si la propriété AutoGenerateEditButton, AutoGenerateDeleteButton ou AutoGenerateSelectButton a **true**, respectivement.

> [!NOTE]
> Insertion d’enregistrements dans la source de données n’est pas directement pris en charge par le contrôle GridView. Toutefois, il est possible d’insérer des enregistrements à l’aide du contrôle GridView conjointement avec le contrôle DetailsView ou le contrôle FormView.


Au lieu d’afficher tous les enregistrements dans la source de données en même temps, le contrôle GridView peut scinder automatiquement les enregistrements en pages. Pour activer la pagination, définissez la propriété AllowPaging **true**.

## <a name="customizing-the-user-interface"></a>Personnalisation de l’Interface utilisateur

Vous pouvez personnaliser l’apparence du contrôle GridView en définissant les propriétés de style pour les différentes parties du contrôle. Le tableau suivant répertorie les propriétés de style différent.

| **Propriété de style** | **Description** |
| --- | --- |
| AlternatingRowStyle | Les paramètres de style pour les lignes de données en alternance dans le contrôle GridView. Lorsque cette propriété est définie, les lignes de données sont affichées en alternant entre les paramètres RowStyle et **AlternatingRowStyle** paramètres. |
| EditRowStyle | Les paramètres de style pour la ligne en cours de modification dans le contrôle GridView. |
| EmptyDataRowStyle | Les paramètres de style pour la ligne de données vide affichée dans le contrôle GridView lors de la source de données ne contient pas d’enregistrements. |
| FooterStyle | Les paramètres de style pour la ligne de pied de page du contrôle GridView. |
| HeaderStyle | Les paramètres de style pour la ligne d’en-tête du contrôle GridView. |
| PagerStyle | Les paramètres de style pour la ligne du pagineur du contrôle GridView. |
| RowStyle | Les paramètres de style pour les lignes de données dans le contrôle GridView. Lorsque le **AlternatingRowStyle** est également définie, les lignes de données sont affichées en alternant entre le **RowStyle** paramètres et la **AlternatingRowStyle** paramètres. |
| SelectedRowStyle | Les paramètres de style pour la ligne sélectionnée dans le contrôle GridView. |

Vous pouvez également afficher ou masquer des parties différentes du contrôle. Le tableau suivant répertorie les propriétés qui contrôlent les parties sont affichés ou masqués.

| **Property** | **Description** |
| --- | --- |
| ShowFooter | Affiche ou masque la section de pied de page du contrôle GridView. |
| ShowHeader | Affiche ou masque la section d’en-tête du contrôle GridView. |

### <a name="events"></a>Événements

Le contrôle GridView fournit plusieurs événements que vous pouvez programmer. Cela vous permet de vous permet d’exécuter une routine personnalisée chaque fois qu’un événement se produit. Le tableau suivant répertorie les événements pris en charge par le contrôle GridView.

| **Event** | **Description** |
| --- | --- |
| PageIndexChanged | Se produit lorsque l’utilisateur clique sur un des boutons de pagineur, mais une fois que le contrôle GridView gère l’opération de pagination. Cet événement est couramment utilisé lorsque vous avez besoin effectuer une tâche une fois que l’utilisateur navigue vers une autre page dans le contrôle. |
| PageIndexChanging | Se produit lorsque l’utilisateur clique sur un des boutons de pagineur, mais avant que le contrôle GridView contrôle l’opération de pagination. Cet événement est souvent utilisé pour annuler l’opération de pagination. |
| RowCancelingEdit | Se produit lorsque l’utilisateur clique sur le bouton d’annulation d’une ligne, mais avant que le contrôle GridView quitte le mode édition. Cet événement est souvent utilisé pour arrêter l’opération d’annulation. |
| RowCommand | Se produit lorsqu’un clic est effectué dans le contrôle GridView. Cet événement est souvent utilisé pour effectuer une tâche lorsqu’un clic est effectué dans le contrôle. |
| Syllabusgrid_rowcreated | Se produit lorsqu’une nouvelle ligne est créée dans le contrôle GridView. Cet événement est souvent utilisé pour modifier le contenu d’une ligne lorsque la ligne est créée. |
| RowDataBound | Se produit lorsqu’une ligne de données est liée aux données dans le contrôle GridView. Cet événement est souvent utilisé pour modifier le contenu d’une ligne lorsque la ligne est liée aux données. |
| RowDeleted | Se produit lorsque l’utilisateur clique sur le bouton Supprimer d’une ligne, mais une fois que le contrôle GridView supprime l’enregistrement de la source de données. Cet événement est souvent utilisé pour vérifier les résultats de l’opération de suppression. |
| RowDeleting | Se produit lorsque l’utilisateur clique sur le bouton Supprimer d’une ligne, mais avant le GridView contrôle supprime l’enregistrement à partir de la source de données. Cet événement est souvent utilisé pour annuler l’opération de suppression. |
| RowEditing | Se produit lorsque l’utilisateur clique sur le bouton de modification d’une ligne, mais avant que le contrôle GridView contrôle en mode édition. Cet événement est souvent utilisé pour annuler l’opération de modification. |
| RowUpdated | Se produit lorsque l’utilisateur clique sur le bouton de mise à jour d’une ligne, mais une fois que le contrôle GridView met à jour la ligne. Cet événement est souvent utilisé pour vérifier les résultats de l’opération de mise à jour. |
| RowUpdating | Se produit lorsque l’utilisateur clique sur le bouton de mise à jour d’une ligne, mais avant le GridView contrôle met à jour la ligne. Cet événement est souvent utilisé pour annuler l’opération de mise à jour. |
| SelectedIndexChanged | Se produit lorsque l’utilisateur clique sur le bouton de sélection d’une ligne, mais une fois que le contrôle GridView gère l’opération de sélection. Cet événement est souvent utilisé pour exécuter une tâche lorsqu’une ligne est sélectionnée dans le contrôle. |
| SelectedIndexChanging | Se produit lorsque l’utilisateur clique sur le bouton de sélection d’une ligne, mais avant que le contrôle GridView contrôle l’opération select. Cet événement est souvent utilisé pour annuler l’opération de sélection. |
| Triées | Se produit lorsque l’utilisateur clique sur le lien hypertexte pour trier une colonne, mais une fois que le contrôle GridView gère l’opération de tri. Cet événement est généralement utilisé pour effectuer une tâche une fois que l’utilisateur clique sur un lien hypertexte pour trier une colonne. |
| Tri | Se produit lorsque l’utilisateur clique sur le lien hypertexte pour trier une colonne, mais avant que le contrôle GridView contrôle l’opération de tri. Cet événement est souvent utilisé pour annuler l’opération de tri ou exécuter une routine de tri personnalisée. |

## <a name="formview"></a>FormView

Le contrôle FormView est utilisé pour afficher un enregistrement unique d’une source de données. Il est semblable au contrôle DetailsView, mais il affiche des modèles définis par l’utilisateur au lieu de champs de ligne. Création de vos propres modèles, vous donne plus de souplesse pour contrôler la façon dont les données sont affichées. Le contrôle FormView prend en charge les fonctionnalités suivantes :

- Liaison aux données des contrôles de source, tels que SqlDataSource et ObjectDataSource.
- Fonctionnalités intégrées d’insertion.
- Intégré à jour et suppression de capacités.
- Fonctionnalités intégrées de pagination.
- L’accès par programme au modèle objet FormView à définir dynamiquement les propriétés, gérer des événements et ainsi de suite.
- Apparence personnalisable grâce aux modèles définis par l’utilisateur, les thèmes et styles.

## <a name="templates"></a>Modèles

Pour le contrôle FormView afficher le contenu, vous devez créer des modèles pour les différentes parties du contrôle. La plupart des modèles sont facultatifs ; Toutefois, vous devez créer un modèle pour le mode dans lequel le contrôle est configuré. Par exemple, un contrôle FormView qui prend en charge d’insertion d’enregistrements doit avoir un modèle d’élément insert défini. Le tableau suivant répertorie les différents modèles que vous pouvez créer.

| **Type de modèle** | **Description** |
| --- | --- |
| EditItemTemplate | Définit le contenu de la ligne de données lorsque le contrôle FormView est en mode édition. Ce modèle contient généralement des contrôles d’entrée et des boutons de commande avec lequel l’utilisateur peut modifier un enregistrement existant. |
| EmptyDataTemplate | Définit le contenu de la ligne de données vide affichée lorsque le contrôle FormView est lié à une source de données qui ne contient-elle pas d’enregistrements. Ce modèle contient généralement le contenu pour avertir l’utilisateur que la source de données ne contient pas d’enregistrements. |
| FooterTemplate | Définit le contenu de la ligne de pied de page. Ce modèle contient généralement le contenu supplémentaire que vous souhaitez afficher dans la ligne de pied de page. En guise d’alternative, vous pouvez simplement spécifier le texte à afficher dans la ligne de pied de page en définissant la propriété de texte pied page. |
| HeaderTemplate | Définit le contenu de la ligne d’en-tête. Ce modèle contient généralement le contenu supplémentaire que vous souhaitez afficher dans la ligne d’en-tête. En guise d’alternative, vous pouvez simplement spécifier le texte à afficher dans la ligne d’en-tête en définissant la propriété HeaderText. |
| ItemTemplate | Définit le contenu de la ligne de données lorsque le contrôle FormView est en mode lecture seule. Ce modèle contient généralement le contenu à afficher les valeurs d’un enregistrement existant. |
| InsertItemTemplate | Définit le contenu de la ligne de données lorsque le contrôle FormView est en mode insertion. Ce modèle contient généralement des contrôles d’entrée et des boutons de commande avec lequel l’utilisateur peut ajouter un nouvel enregistrement. |
| PagerTemplate | Définit le contenu de la ligne du pagineur affichée lorsque la fonctionnalité de pagination est activée (lorsque la propriété AllowPaging a la valeur **true**). Ce modèle contient généralement des contrôles avec lequel l’utilisateur d’accéder à un autre enregistrement. |

Les contrôles d’entrée dans le modèle d’élément de modification et le modèle d’élément insert peuvent être liés aux champs d’une source de données à l’aide d’une expression de liaison bidirectionnelle. Ainsi, le contrôle FormView à extraire automatiquement les valeurs du contrôle d’entrée pour une mise à jour ou opération d’insertion. Expressions de liaison bidirectionnelles permettent également des contrôles d’entrée dans un modèle d’élément de modification pour afficher automatiquement les valeurs de champ d’origine.

### <a name="binding-to-data"></a>Liaison de données

Le contrôle FormView peut être lié à un contrôle de source de données (tel que **SqlDataSource**, AccessDataSource, **ObjectDataSource** et ainsi de suite), ou à une source de données qui implémente le Interface System.Collections.IEnumerable (par exemple, System.Data.DataView, System.Collections.ArrayList et System.Collections.Hashtable). Pour lier le contrôle FormView pour le type de source de données approprié, utilisez une des méthodes suivantes :

- Pour lier à un contrôle de source de données, définissez la propriété DataSourceID du contrôle FormView à la valeur d’ID du contrôle de source de données. Le contrôle FormView automatiquement lie au contrôle de source de données spécifié et peut tirer parti de la source de données des fonctionnalités de contrôle pour effectuer une insertion, mise à jour, suppression et la fonctionnalité de pagination. Il s’agit de la méthode recommandée pour lier aux données.
- Pour lier à une source de données qui implémente le **System.Collections.IEnumerable** interface et définir par programme la propriété DataSource du contrôle FormView à la source de données, puis appelez la méthode DataBind. Lorsque vous utilisez cette méthode, le contrôle FormView ne fournit pas intégrées d’insertion, mise à jour, suppression et la fonctionnalité de pagination. Vous devez fournir cette fonctionnalité à l’aide de l’événement approprié.

## <a name="data-operations"></a>Opérations de données

Le contrôle FormView fournit plusieurs fonctionnalités intégrées qui permettent à l’utilisateur à mettre à jour, supprimer, insérer et parcourir les éléments dans le contrôle. Lorsque le contrôle FormView est lié à un contrôle de source de données, le contrôle FormView permettre tirer parti de la source de données de fonctionnalités du contrôle et fournir automatique mise à jour, suppression, l’insertion et d’échange. Le contrôle FormView prennent en charge update, delete, insert et les opérations de pagination avec d’autres types de sources de données ; Toutefois, vous devez fournir un gestionnaire d’événements approprié avec l’implémentation de ces opérations.

Étant donné que le contrôle FormView utilise des modèles, il ne fournit pas afin de générer automatiquement des boutons de commande pour effectuer la mise à jour, la suppression ou l’insertion d’opérations. Vous devez inclure manuellement ces boutons de commande dans le modèle approprié. Le contrôle FormView reconnaît certains boutons qui ont leur **CommandName** propriétés définies sur des valeurs spécifiques. Le tableau suivant répertorie les boutons de commande que le contrôle FormView reconnaît.

| **Button** | **Valeur de CommandName** | **Description** |
| --- | --- | --- |
| Annuler | « Annuler » | Utilisé dans la mise à jour ou d’opérations d’insertion pour annuler l’opération et ignorer les valeurs entrées par l’utilisateur. Le contrôle FormView retourne ensuite au mode spécifié par la propriété DefaultMode. |
| Supprimer | "Delete" | Utilisé dans les opérations de suppression pour supprimer l’enregistrement affiché à partir de la source de données. Déclenche les événements ItemDeleting et ItemDeleted. |
| Modifier | « Modifier » | Utilisé dans les opérations de mise à jour pour placer le contrôle FormView en mode édition. Le contenu spécifié dans le **EditItemTemplate** propriété s’affiche pour la ligne de données. |
| Insert | « Insertion » | Utilisé dans les opérations d’insertion pour tenter d’insérer un nouvel enregistrement dans la source de données à l’aide des valeurs fournies par l’utilisateur. Déclenche les événements ItemInserting et ItemInserted. |
| Nouveau | « Nouveau » | Utilisé dans les opérations d’insertion pour placer le contrôle FormView en mode insertion. Le contenu spécifié dans le **InsertItemTemplate** propriété s’affiche pour la ligne de données. |
| Page | « Page » | Utilisé dans les opérations de pagination pour représenter un bouton dans la ligne du pagineur qui effectue la pagination. Pour spécifier l’opération de pagination, définissez la **CommandArgument** propriété du bouton « Suivant », « Précédent », « First », « Dernier » ou l’index de la page vers laquelle naviguer. Déclenche les événements PageIndexChanging et PageIndexChanged. |
| Mise à jour | « Update » | Utilisé dans les opérations de mise à jour pour tenter de mettre à jour l’enregistrement affiché dans la source de données avec les valeurs fournies par l’utilisateur. Déclenche les événements ItemUpdating et ItemUpdated est attendu. |

Contrairement à la suppression bouton (qui supprime immédiatement l’enregistrement affiché), lorsque l’utilisateur clique sur le bouton Modifier ou nouveau, le contrôle FormView contrôle passe à modifier ou mode d’insertion respectivement. En mode édition, le contenu de la **EditItemTemplate** propriété s’affiche pour l’élément de données actuel. En règle générale, le modèle d’élément de modification est défini tel que le bouton Modifier est remplacé par une mise à jour et un bouton Annuler. Les contrôles d’entrée qui sont appropriées pour le type de données du champ (par exemple, une zone de texte ou un contrôle de case à cocher) sont également généralement affichés avec la valeur d’un champ pour l’utilisateur à modifier. En cliquant sur le bouton de mise à jour met à jour l’enregistrement dans la source de données, alors que le bouton Annuler abandonne toutes les modifications.

De même, le contenu de la **InsertItemTemplate** propriété s’affiche pour l’élément de données lorsque le contrôle est en mode insertion. Le modèle d’élément insert est généralement défini telles que le bouton Nouveau est remplacé par une instruction Insert et un bouton Annuler, et les contrôles d’entrée vides sont affichés pour l’utilisateur à entrer les valeurs pour le nouvel enregistrement. En cliquant sur le bouton Insérer d’insère l’enregistrement dans la source de données, alors que le bouton Annuler abandonne toutes les modifications.

Le contrôle FormView fournit une fonctionnalité de pagination qui permet à l’utilisateur accéder à d’autres enregistrements dans la source de données. Lorsque activé, une ligne de pagineur est affichée dans le contrôle FormView qui contient les contrôles de navigation de page. Pour activer la pagination, définissez la **AllowPaging** propriété **true**. Vous pouvez personnaliser la ligne du pagineur en définissant les propriétés des objets contenus dans le PagerStyle et la propriété PagerSettings. Au lieu d’utiliser l’interface utilisateur de la ligne de pagineur intégrés, vous pouvez créer votre propre interface utilisateur à l’aide de la **PagerTemplate** propriété.

## <a name="customizing-the-user-interface"></a>Personnalisation de l’Interface utilisateur

Vous pouvez personnaliser l’apparence du contrôle FormView en définissant les propriétés de style pour les différentes parties du contrôle. Le tableau suivant répertorie les propriétés de style différent.

| **Propriété de style** | **Description** |
| --- | --- |
| EditRowStyle | Les paramètres de style pour la ligne de données lorsque le contrôle FormView est en mode d’édition. |
| EmptyDataRowStyle | Les paramètres de style pour la ligne de données vide affichée dans le contrôle FormView lorsque la source de données ne contient pas d’enregistrements. |
| FooterStyle | Les paramètres de style pour la ligne de pied de page du contrôle FormView. |
| HeaderStyle | Les paramètres de style pour la ligne d’en-tête du contrôle FormView. |
| InsertRowStyle | Les paramètres de style pour la ligne de données lorsque le contrôle FormView est en mode d’insertion. |
| PagerStyle | Les paramètres de style pour la ligne du pagineur affichés dans le contrôle FormView lorsque la fonctionnalité de pagination est activée. |
| RowStyle | Les paramètres de style pour la ligne de données lorsque le contrôle FormView est en mode lecture seule. |

## <a name="events"></a>Événements

Le contrôle FormView fournit plusieurs événements que vous pouvez programmer. Cela vous permet de vous permet d’exécuter une routine personnalisée chaque fois qu’un événement se produit. Le tableau suivant répertorie les événements pris en charge par le contrôle FormView.

| **Event** | **Description** |
| --- | --- |
| ItemCommand | Se produit lorsque l’utilisateur clique sur un bouton dans un contrôle FormView. Cet événement est souvent utilisé pour effectuer une tâche lorsqu’un clic est effectué dans le contrôle. |
| ItemCreated | Se produit une fois que tous les objets FormViewRow sont créés dans le contrôle FormView. Cet événement est souvent utilisé pour modifier les valeurs d’un enregistrement avant son affichage. |
| ItemDeleted | Se produit lorsqu’un bouton Supprimer (un bouton avec son **CommandName** propriété la valeur « Delete ») est activé, mais une fois que le contrôle FormView supprime l’enregistrement de la source de données. Cet événement est souvent utilisé pour vérifier les résultats de l’opération de suppression. |
| ItemDeleting | Se produit lorsque l’utilisateur clique sur un bouton Supprimer, mais avant le FormView contrôle supprime l’enregistrement à partir de la source de données. Cet événement est souvent utilisé pour annuler l’opération de suppression. |
| ItemInserted | Se produit lorsqu’un bouton Insérer (un bouton avec son **CommandName** propriété la valeur « Insert ») est activé, mais une fois que le contrôle FormView insère l’enregistrement. Cet événement est souvent utilisé pour vérifier les résultats de l’opération d’insertion. |
| ItemInserting | Se produit lorsque l’utilisateur clique sur un bouton Insérer, mais avant le FormView contrôle insère l’enregistrement. Cet événement est souvent utilisé pour annuler l’opération d’insertion. |
| ItemUpdated est attendu | Se produit lorsqu’un bouton de mise à jour (un bouton avec son **CommandName** propriété la valeur « Update ») est activé, mais une fois que le contrôle FormView met à jour la ligne. Cet événement est souvent utilisé pour vérifier les résultats de l’opération de mise à jour. |
| ItemUpdating | Se produit lorsque l’utilisateur clique sur un bouton de mise à jour, mais avant le FormView contrôle met à jour l’enregistrement. Cet événement est souvent utilisé pour annuler l’opération de mise à jour. |
| ModeChanged | Se produit après que le contrôle FormView modifie des modes (pour le mode édition, insert ou en lecture seule). Cet événement est souvent utilisé pour effectuer une tâche lorsque le contrôle FormView modifie des modes. |
| ModeChanging | Se produit avant que le contrôle FormView modifie des modes (pour le mode édition, insert ou en lecture seule). Cet événement est souvent utilisé pour annuler un changement de mode. |
| PageIndexChanged | Se produit lorsque l’utilisateur clique sur un des boutons de pagineur, mais une fois que le contrôle FormView gère l’opération de pagination. Cet événement est couramment utilisé lorsque vous avez besoin effectuer une tâche une fois que l’utilisateur navigue vers un autre enregistrement dans le contrôle. |
| PageIndexChanging | Se produit lorsque l’utilisateur clique sur un des boutons de pagineur, mais avant le FormView contrôle gère l’opération de pagination. Cet événement est souvent utilisé pour annuler l’opération de pagination. |

## <a name="detailsview"></a>Contrôle DetailsView

Le contrôle DetailsView est utilisé pour afficher un enregistrement unique d’une source de données dans une table, où chaque champ de l’enregistrement s’affiche dans une ligne de la table. Il peut être utilisé en association avec un contrôle GridView pour les scénarios maître / détail. Le contrôle DetailsView prend en charge les fonctionnalités suivantes :

- Liaison aux données des contrôles de source, tels que SqlDataSource.
- Fonctionnalités intégrées d’insertion.
- Intégré à jour et suppression de capacités.
- Fonctionnalités intégrées de pagination.
- L’accès par programme au modèle d’objet DetailsView à définir dynamiquement les propriétés, gérer des événements et ainsi de suite.
- Apparence personnalisable grâce aux thèmes et styles.

## <a name="row-fields"></a>Champs de ligne

Chaque ligne de données dans le contrôle DetailsView est créé en déclarant un contrôle de champ. Types de champs de ligne différentes déterminent le comportement des lignes dans le contrôle. Contrôles de champ dérivent DataControlField. Le tableau suivant répertorie les types de champs de ligne différente qui peuvent être utilisés.

| **Type de champ de colonne** | **Description** |
| --- | --- |
| BoundField | Affiche la valeur d’un champ dans une source de données sous forme de texte. |
| ButtonField | Affiche un bouton de commande dans le contrôle DetailsView. Cela vous permet d’afficher une ligne avec un contrôle bouton personnalisé, par exemple un bouton Ajouter ou supprimer. |
| CheckBoxField | Affiche une case à cocher dans le contrôle DetailsView. Ce type de champ de ligne est couramment utilisé pour afficher les champs avec une valeur booléenne. |
| CommandField | Affiche les commandes intégrées boutons permettent d’effectuer modifier, insérer ou supprimer des opérations dans le contrôle DetailsView. |
| HyperLinkField | Affiche la valeur d’un champ dans une source de données sous la forme d’un lien hypertexte. Ce type de champ de ligne vous permet de lier un deuxième champ vers les URL du lien hypertexte. |
| ImageField | Affiche une image dans le contrôle DetailsView. |
| TemplateField | Affiche le contenu défini par l’utilisateur pour une ligne dans le contrôle DetailsView selon un modèle spécifié. Ce type de champ de ligne vous permet de créer un champ de ligne personnalisée. |

Par défaut, la propriété AutoGenerateRows a la valeur **true**, ce qui génère automatiquement un objet de champ de ligne dépendant pour chaque champ d’un type pouvant être lié dans la source de données. Les types valides pouvant être liés sont String, DateTime, Decimal, Guid et l’ensemble des types primitifs. Chaque champ est ensuite affiché dans une ligne sous forme de texte, dans l’ordre dans lequel chaque champ apparaît dans la source de données.

Génération automatique des lignes fournit un moyen simple et rapide d’afficher chaque champ dans l’enregistrement. Toutefois, pour utiliser le contrôle DetailsView contrôle des fonctionnalités avancées que vous devez déclarer explicitement les champs de ligne à inclure dans le contrôle DetailsView. Pour déclarer les champs de ligne, attribuez d’abord le **AutoGenerateRows** propriété **false**. Ensuite, ajoutez l’ouverture et de fermeture  **&lt;champs&gt;**  balises situées entre les balises d’ouverture et de fermeture du contrôle DetailsView. Enfin, répertoriez les champs de ligne que vous souhaitez inclure entre les balises  **&lt;champs&gt;**  balises. Les champs de ligne spécifiés sont ajoutés à la collection de champs dans l’ordre indiqué. Le **champs** collection vous permet de gérer par programme les champs de ligne dans le contrôle DetailsView.

> [!NOTE]
> Les champs ne sont pas ajoutés à la collection de champs de ligne générés automatiquement.


## <a name="binding-to-data"></a>Liaison de données

Le contrôle DetailsView peut être lié à un contrôle de source de données, tel que **SqlDataSource** ou AccessDataSource, ou à une source de données qui implémente l’interface System.Collections.IEnumerable, telles que System.Data.DataView, System.Collections.ArrayList et System.Collections.Hashtable.

Pour lier le contrôle DetailsView pour le type de source de données approprié, utilisez une des méthodes suivantes :

- Pour lier à un contrôle de source de données, définissez la propriété DataSourceID du contrôle DetailsView à la valeur d’ID du contrôle de source de données. Le contrôle DetailsView est automatiquement lié au contrôle de source de données spécifié. Il s’agit de la méthode recommandée pour lier aux données.
- Pour lier à une source de données qui implémente le **System.Collections.IEnumerable** interface et définir par programme la propriété DataSource du contrôle DetailsView à la source de données, puis appelez la méthode DataBind.

## <a name="security"></a>Sécurité

Ce contrôle peut être utilisé pour afficher l’entrée d’utilisateur, ce qui peut inclure un script client malveillant. Vérifiez toutes les informations envoyées par un client de script exécutable, des instructions SQL ou autre code avant de les afficher dans votre application. ASP.NET fournit une fonctionnalité de validation de demande d’entrée pour bloquer les scripts et le HTML dans l’entrée d’utilisateur.

## <a name="data-operations"></a>Opérations de données

Le contrôle DetailsView fournit des fonctionnalités intégrées qui permettent à l’utilisateur à mettre à jour, supprimer, insérer et parcourir les éléments dans le contrôle. Lorsque le contrôle DetailsView est lié à un contrôle de source de données, le contrôle DetailsView permettre tirer parti de la source de données de fonctionnalités du contrôle et fournir automatique mise à jour, suppression, l’insertion et d’échange.

Le contrôle DetailsView peut fournir la prise en charge pour la mise à jour, delete, insert et les opérations de pagination avec d’autres types de sources de données ; Toutefois, vous devez fournir l’implémentation de ces opérations dans un gestionnaire d’événements approprié.

Le contrôle DetailsView peut ajouter automatiquement un **CommandField** champ de ligne avec un bouton Modifier, supprimer ou nouveau en définissant les propriétés AutoGenerateEditButton, AutoGenerateDeleteButton ou AutoGenerateInsertButton à **true**, respectivement. Contrairement à la suppression bouton (qui supprime immédiatement l’enregistrement sélectionné), lorsque l’utilisateur clique sur le bouton Modifier ou nouveau, le contrôle passe à l’édition de DetailsView ou mode insertion, respectivement. En mode édition, le bouton Modifier est remplacé par une mise à jour et un bouton Annuler. Les contrôles d’entrée qui sont appropriées pour le type de données du champ (par exemple, une zone de texte ou un contrôle de case à cocher) sont affichés avec la valeur d’un champ pour l’utilisateur à modifier. En cliquant sur le bouton de mise à jour met à jour l’enregistrement dans la source de données, alors que le bouton Annuler abandonne toutes les modifications. De même, en mode d’insertion, le bouton Nouveau est remplacé par une instruction Insert et un bouton Annuler, et les contrôles d’entrée vides sont affichés pour l’utilisateur à entrer les valeurs pour le nouvel enregistrement.

Le contrôle DetailsView fournit une fonctionnalité de pagination qui permet à l’utilisateur accéder à d’autres enregistrements dans la source de données. Lorsque activé, les contrôles de navigation de page sont affichés dans une ligne du pagineur. Pour activer la pagination, définissez la propriété AllowPaging **true**. La ligne du pagineur peut être personnalisée en utilisant les propriétés PagerStyle et PagerSettings.

## <a name="customizing-the-user-interface"></a>Personnalisation de l’Interface utilisateur

Vous pouvez personnaliser l’apparence du contrôle DetailsView en définissant les propriétés de style pour les différentes parties du contrôle. Le tableau suivant répertorie les propriétés de style différent.

| **Propriété de style** | **Description** |
| --- | --- |
| AlternatingRowStyle | Les paramètres de style pour les lignes de données en alternance dans le contrôle DetailsView. Lorsque cette propriété est définie, les lignes de données sont affichées en alternant entre les paramètres RowStyle et **AlternatingRowStyle** paramètres. |
| CommandRowStyle | Les paramètres de style pour la ligne contenant les boutons de commande intégrés dans le contrôle DetailsView. |
| EditRowStyle | Les paramètres de style pour les lignes de données lorsque le contrôle DetailsView est en mode d’édition. |
| EmptyDataRowStyle | Les paramètres de style pour la ligne de données vide affichée dans le contrôle DetailsView lorsque la source de données ne contient pas d’enregistrements. |
| FooterStyle | Les paramètres de style pour la ligne de pied de page du contrôle DetailsView. |
| HeaderStyle | Les paramètres de style pour la ligne d’en-tête du contrôle DetailsView. |
| InsertRowStyle | Les paramètres de style pour les lignes de données lorsque le contrôle DetailsView est en mode d’insertion. |
| PagerStyle | Les paramètres de style pour la ligne du pagineur du contrôle DetailsView. |
| RowStyle | Les paramètres de style pour les lignes de données dans le contrôle DetailsView. Lorsque le **AlternatingRowStyle** est également définie, les lignes de données sont affichées en alternant entre le **RowStyle** paramètres et la **AlternatingRowStyle** paramètres. |
| FieldHeaderStyle | Les paramètres de style pour la colonne de l’en-tête du contrôle DetailsView. |

## <a name="events"></a>Événements

Le contrôle DetailsView fournit plusieurs événements que vous pouvez programmer. Cela vous permet de vous permet d’exécuter une routine personnalisée chaque fois qu’un événement se produit. Le tableau suivant répertorie les événements pris en charge par le contrôle DetailsView. Le contrôle DetailsView hérite également de ces événements à partir de ses classes de base : liaison de données, liée aux données, Disposed, Init, Load, PreRender et rendu.

| **Event** | **Description** |
| --- | --- |
| ItemCommand | Se produit lorsqu’un clic est effectué dans le contrôle DetailsView. |
| ItemCreated | Se produit une fois que tous les objets DetailsViewRow sont créés dans le contrôle DetailsView. Cet événement est souvent utilisé pour modifier les valeurs d’un enregistrement avant son affichage. |
| ItemDeleted | Se produit lorsque l’utilisateur clique sur un bouton Supprimer, mais une fois que le contrôle DetailsView supprime l’enregistrement de la source de données. Cet événement est souvent utilisé pour vérifier les résultats de l’opération de suppression. |
| ItemDeleting | Se produit lorsque l’utilisateur clique sur un bouton Supprimer, mais avant le contrôle DetailsView contrôle supprime l’enregistrement à partir de la source de données. Cet événement est souvent utilisé pour annuler l’opération de suppression. |
| ItemInserted | Se produit lorsque l’utilisateur clique sur un bouton Insérer, mais une fois que le contrôle DetailsView insère l’enregistrement. Cet événement est souvent utilisé pour vérifier les résultats de l’opération d’insertion. |
| ItemInserting | Se produit lorsque l’utilisateur clique sur un bouton Insérer, mais avant le contrôle DetailsView contrôle insère l’enregistrement. Cet événement est souvent utilisé pour annuler l’opération d’insertion. |
| ItemUpdated est attendu | Se produit lorsque l’utilisateur clique sur un bouton de mise à jour, mais une fois que le contrôle DetailsView met à jour la ligne. Cet événement est souvent utilisé pour vérifier les résultats de l’opération de mise à jour. |
| ItemUpdating | Se produit lorsque l’utilisateur clique sur un bouton de mise à jour, mais avant le contrôle DetailsView contrôle met à jour l’enregistrement. Cet événement est souvent utilisé pour annuler l’opération de mise à jour. |
| ModeChanged | Se produit une fois que le contrôle DetailsView modifie des modes (mode édition, insert ou en lecture seule). Cet événement est souvent utilisé pour effectuer une tâche lorsque le contrôle DetailsView modifie des modes. |
| ModeChanging | Se produit avant que le contrôle DetailsView modifie des modes (mode édition, insert ou en lecture seule). Cet événement est souvent utilisé pour annuler un changement de mode. |
| PageIndexChanged | Se produit lorsque l’utilisateur clique sur un des boutons de pagineur, mais une fois que le contrôle DetailsView gère l’opération de pagination. Cet événement est couramment utilisé lorsque vous avez besoin effectuer une tâche une fois que l’utilisateur navigue vers un autre enregistrement dans le contrôle. |
| PageIndexChanging | Se produit lorsque l’utilisateur clique sur un des boutons de pagineur, mais avant que le contrôle DetailsView contrôle l’opération de pagination. Cet événement est souvent utilisé pour annuler l’opération de pagination. |

## <a name="the-menu-control"></a>Le contrôle de Menu

Le contrôle de Menu dans ASP.NET 2.0 est conçu pour être un système complet. Il peut être lié aux données facilement à des sources de données hiérarchiques tels que SiteMapDataSource.

Une structure de contrôles de Menu peut être définie de manière déclarative ou dynamique et se compose d’un nœud racine unique et un nombre quelconque de sous-nœuds. Le code suivant définit manière déclarative un menu pour le contrôle de Menu.

[!code-aspx[Main](data-bound-controls/samples/sample4.aspx)]

Dans l’exemple ci-dessus, le nœud Home.aspx est le nœud racine. Tous les autres nœuds sont imbriqués dans le nœud racine à différents niveaux.

Il existe deux types de menus qui peut effectuer le rendu du contrôle de Menu ; les menus statiques et dynamiques. Les menus statiques sont constituées d’éléments de menu sont toujours visibles. Menus dynamiques se composent des éléments de menu qui sont visibles uniquement lorsque l’utilisateur pointe dessus avec la souris. Les clients peuvent souvent confondez menus statiques avec les menus définies de façon déclarative et dynamique des menus qui sont liés aux données lors de l’exécution. En fait, les menus dynamiques et statiques ne sont pas liés à la méthode de remplissage. Les termes du contrat *statique* et *dynamique* faire uniquement si le menu est statiquement affichée par défaut ou uniquement affiché lorsque l’utilisateur effectue une action.

Le **StaticDisplayLevels** propriété est utilisée pour configurer le nombre de niveaux du menu est statiques et par conséquent s’affichent par défaut. Dans l’exemple ci-dessus, en définissant le **StaticDisplayLevels** propriété à une valeur de 2 provoquerait le menu statique affiche le nœud d’accueil, le nœud de la musique et le nœud de films. Tous les autres nœuds dynamiquement affiche lorsque l’utilisateur pointe sur le nœud parent.

Le **MaximumDynamicDisplayLevels** propriété configure le nombre maximal de niveaux dynamiques du menu est capable d’afficher. Les menus dynamiques à un niveau supérieur à la valeur spécifiée par la **MaximumDynamicDisplayLevels** propriété sont ignorées.

> [!NOTE]
> Il est presque certain que vous pouvez rencontrer des situations où les menus n’apparaissent pas pour effectuer le rendu en raison de la propriété MaximumDynamicDisplayLevels. Dans ce cas, assurez-vous que la propriété est définie suffisamment pour permettre l’affichage des menus de clients.


## <a name="data-binding-the-menu-control"></a>Le contrôle de Menu de liaison de données

Le contrôle de Menu peut être lié à n’importe quelle source de données hiérarchiques tels que SiteMapDataSource ou XMLDataSource. SiteMapDataSource est le plus communément utilisée pour la liaison de données à un contrôle de Menu, car il flux sur le fichier Web.sitemap et son schéma fournit une API connue pour le contrôle de Menu. La liste ci-dessous montre un simple fichier Web.sitemap.

[!code-xml[Main](data-bound-controls/samples/sample5.xml)]

Notez qu’il n'existe qu’un seul élément siteMapNode racine, dans ce cas, l’élément « accueil ». Plusieurs attributs peuvent être configurées pour chaque siteMapNode. Les attributs couramment utilisées sont :

- **URL** Spécifie l’URL à afficher lorsqu’un utilisateur clique sur l’élément de menu. Si cet attribut n’est pas présent, le nœud simplement valide lorsque vous cliquez sur.
- **titre** Spécifie le texte qui s’affiche sur l’élément de menu.
- **Description** utilisé en tant que la documentation pour le nœud. Affiche également une info-bulle lorsque la souris se trouve sur le nœud.
- **siteMapFile** autorise les sitemaps imbriquées. Cet attribut doit pointer vers un fichier de sitemap ASP.NET bien formé.
- **rôles** permet l’apparence d’un nœud pour être contrôlé par l’ajustement de la sécurité ASP.NET.

Notez que si ces attributs sont tous facultatifs, le comportement du menu peut-être pas ce qui est attendu si elles ne sont pas spécifiées. Par exemple, si le *url* attribut est spécifié, mais le *description* attribut n’est pas, le nœud ne sera pas visible, et il n’y a aucun moyen pour accéder à l’URL spécifiée.

## <a name="controlling-a-menus-operation"></a>Contrôle d’une opération de Menus

Il existe plusieurs propriétés qui affectent le fonctionnement d’un contrôle Menu ASP.NET ; le **Orientation** propriété, le **DisappearAfter** propriété, le **StaticItemFormatString** propriété et le **StaticPopoutImageUrl**propriété sont quelques exemples de ces.

- Le **Orientation** peut être définie avec la valeur *horizontal* ou *vertical* et détermine si les éléments de menu statiques sont disposés horizontalement dans une ligne ou verticalement et empilés sur un autre. Cette propriété n’affecte pas les menus dynamiques.
- Le **DisappearAfter** propriété configure la durée pendant laquelle un menu dynamique doit rester visible une fois que la souris a été déplacé en dehors de celui-ci. La valeur est spécifiée en millisecondes et la valeur par défaut est 500. Définition de cette propriété à une valeur de -1 entraîne le menu jamais disparaître automatiquement. Dans ce cas, le menu disparaissent uniquement lorsque l’utilisateur clique en dehors du menu.
- Le **StaticItemFormatString** propriété rend facile à maintenir une formulation cohérente dans votre système de menus. Lorsque vous spécifiez cette propriété, *{0}* doit être entré à la place de la description qui apparaît dans la source de données. Par exemple, pour disposer de l’élément de menu à partir de l’exercice 1 prononcer visitez notre Page de produits, etc., vous indiqueriez visitez notre Page pour le StaticItemFormatString de {0}. Lors de l’exécution, ASP.NET remplace toute occurrence de {0} avec la description correcte de l’élément de menu.
- Le **StaticPopoutImageUrl** propriété spécifie l’image qui est utilisée pour indiquer qu’un nœud particulier de menu a des nœuds enfants qui sont accessibles en pointant dessus. Menus dynamiques continue à utiliser l’image par défaut.

## <a name="templated-menu-controls"></a>Contrôles de Menu basé sur un modèle

Le contrôle de Menu est un contrôle basé sur un modèle et permet de deux éléments de personnalisés différents ; le StaticItemTemplate et le DynamicItemTemplate. À l’aide de ces modèles, vous pouvez facilement ajouter des contrôles de serveur ou de contrôles utilisateur à vos menus.

Pour modifier les modèles dans Visual Studio .NET, cliquez sur le bouton de balise active dans le menu et choisissez Modifier les modèles. Ensuite, vous pouvez choisir entre la modification de la StaticItemTemplate ou le DynamicItemTemplate.

Les contrôles ajoutés à la StaticItemTemplate apparaîtra dans le menu statique lorsque le chargement de la page. Les contrôles ajoutés à la DynamicItemTemplate apparaîtra dans tous les menus contextuels.

## <a name="menu-events"></a>Événements de menu

Le contrôle Menu dispose de deux événements qui lui sont spécifiques ; le **MenuItemClicked** et **MenuItemDatabound** événement.

L’événement MenuItemClicked est déclenché lorsque l’utilisateur clique sur un élément de menu. L’événement MenuItemDatabound est déclenché lorsqu’un élément de menu est lié aux données. Le **MenuEventArgs** qui est passé à l’événement Gestionnaire fournit l’accès à l’élément de menu via la propriété d’élément.

## <a name="controlling-a-menus-appearance"></a>Contrôle un aspect de Menus

Vous pouvez également changer l’apparence d’un contrôle de menu à l’aide d’un ou plusieurs des nombreux styles disponibles pour les menus de format. Parmi celles-ci sont **StaticMenuStyle**, **DynamicMenuStyle**, **DynamicMenuItemStyle**, **DynamicSelectedStyle**et **DynamicHoverStyle**. Ces propriétés sont configurées à l’aide d’une chaîne de HTML Style standard. Par exemple, la commande suivante affecte le style de menus dynamiques.

[!code-aspx[Main](data-bound-controls/samples/sample6.aspx)]

> [!NOTE]
> Si vous utilisez l’un des styles de pointage, vous devez ajouter un &lt;head&gt; élément dans la page avec le *runat* élément *server*.


Les contrôles menu prennent également en charge l’utilisation de thèmes d’ASP.NET 2.0.

## <a name="the-treeview-control"></a>Le contrôle TreeView

Le contrôle TreeView affiche des données dans une structure arborescente. Comme avec le contrôle de Menu, il peut être facilement lié aux données d’une source de données hiérarchiques SiteMapDataSource.

La première question que les clients sont susceptibles de poser sur le contrôle TreeView dans ASP.NET 2.0 est ou non il concerne le contrôle TreeView IE WebControl qui était disponible pour ASP.NET 1.x. Il n’est pas le cas. Le contrôle TreeView d’ASP.NET 2.0 a été écrite de bas en haut et offre une amélioration significative sur le contrôle TreeView WebControl Internet Explorer qui était précédemment disponible.

Je ne vais pas en détail comment lier un contrôle TreeView à un plan de site, tel qu’il est exécuté dans la même façon que le contrôle de Menu. Toutefois, le contrôle TreeView a certaines des différences dans la façon dont elle fonctionne.

Par défaut, un contrôle TreeView apparaît complètement développé. Pour modifier le niveau d’expansion lors du chargement initial, modifiez le **ExpandDepth** propriété du contrôle. Cela est particulièrement important dans les cas où le contrôle TreeView est lié aux données lors de l’expansion de nœuds.

## <a name="databinding-the-treeview-control"></a>Liaison de données du contrôle TreeView

Contrairement au contrôle de Menu, le contrôle TreeView se prête bien à gérer de grandes quantités de données. Par conséquent, en plus de la liaison de données à un SiteMapDataSource ou un XMLDataSource, le TreeView est souvent lié aux données d’un jeu de données ou d’autres données relationnelles. Dans les cas où le contrôle TreeView est lié à de grandes quantités de données, il est préférable de lier uniquement aux données qui est réellement visibles dans le contrôle. Vous pouvez ensuite lier des données à des données supplémentaires comme nœuds TreeView sont développées.

Dans ce cas, le **PopulateOnDemand** propriété du contrôle TreeView doit être définie sur *true*. Vous devrez ensuite fournir une implémentation de la **TreeNodePopulate** (méthode).

## <a name="data-binding-without-postback"></a>Liaison sans publication (postback) de données

Notez que lorsque vous développez un nœud dans l’exemple précédent pour la première fois, la page publie et actualise. Vous avez pas un problème dans cet exemple, mais vous pouvez l’imaginer qu’il peut être dans un environnement de production avec une grande quantité de données. Un scénario meilleur serait dans lequel le contrôle TreeView est toujours dynamiquement remplir ses nœuds, mais sans publication au serveur.

En définissant le **PopulateNodesFromClient** et **PopulateOnDemand** propriétés est true, le contrôle TreeView ASP.NET remplirez dynamiquement des nœuds sans publication. Lorsque le nœud parent est développé, une demande de XMLHttp est effectuée à partir du client et l’événement OnTreeNodePopulate est déclenché. Le serveur renvoie un îlot de données XML qui est ensuite utilisé pour les données de lier les nœuds enfants.

ASP.NET crée dynamiquement le code client qui implémente cette fonctionnalité. Le &lt;script&gt; balises contenant le script sont générés pointant vers un fichier AXD. Par exemple, la liste ci-dessous répertorie les liens de script pour le code de script qui génère la demande XMLHttp.

[!code-html[Main](data-bound-controls/samples/sample7.html)]

Si vous recherchez le fichier AXD ci-dessus dans votre navigateur et que vous l’ouvrez, vous verrez le code qui implémente la demande XMLHttp. Cette méthode empêche les clients de modifier le fichier de script.

## <a name="controlling-the-operation-of-the-treeview-control"></a>Contrôle du fonctionnement du contrôle TreeView

Le contrôle TreeView possède plusieurs propriétés qui affectent le fonctionnement du contrôle. Les propriétés évidentes sont la **ShowCheckBoxes**, **ShowExpandCollapse**, et **ShowLines**.

Le **ShowCheckBoxes** propriété affecte ou non de nœuds affichent une case à cocher lors du rendu. Les valeurs valides pour cette propriété sont **aucun**, **racine**, **Parent**, **feuille**, et **tous les**. Ces modifications concernent le contrôle TreeView comme suit :

| **Valeur de propriété** | **Effet** |
| --- | --- |
| Aucune | Les cases à cocher ne sont pas affichés sur les nœuds. Il s'agit du paramètre par défaut. |
| racine | Une case à cocher est affichée uniquement sur le nœud racine. |
| Parent | Une case à cocher est affichée uniquement sur les nœuds dotés de nœuds enfants. Ces nœuds enfants peuvent être nœuds parents ou les nœuds terminaux. |
| Feuille | Une case à cocher s’affiche uniquement sur les nœuds qui n’ont aucuns nœuds enfants. |
| Tout | Une case à cocher s’affiche sur tous les nœuds. |

Lorsque les cases à cocher sont utilisés, le **CheckedNodes** propriété retourne une collection de nœuds TreeView qui sont vérifiées lors de la publication.

Le **ShowExpandCollapse** propriété contrôle l’apparence de l’image de développer/réduire à côté des nœuds racine et le parent. Si cette propriété est définie sur **false**, les nœuds TreeView sont rendus sous forme de liens hypertexte et sont développée réduite en cliquant sur le lien.

Le **ShowLines** propriété contrôle si les lignes sont affichées connexion nœuds parents aux nœuds enfants. Lorsque **false** (la valeur par défaut), aucune ligne n’est affichés. Lorsque **true**, le contrôle TreeView utilisera les images des lignes dans le dossier spécifié par le **LineImagesFolder** propriété.

Pour personnaliser l’apparence des lignes de TreeView, Visual Studio .NET 2005 inclut un outil Concepteur de lignes. Vous pouvez accéder à cet outil à l’aide du bouton de balise active sur le contrôle TreeView comme ci-dessous.


![](data-bound-controls/_static/image1.jpg)

**Figure 1**


Quand sélectionner le **personnaliser les Images de ligne** option de menu, l’outil Concepteur de lignes sera lancé ce qui vous permet de configurer l’apparence des lignes du contrôle TreeView.

## <a name="treeview-events"></a>Événements du contrôle TreeView

Le contrôle TreeView présente les événements uniques suivantes :

- SelectedNodeChanged se produit lorsqu’un nœud est sélectionné en fonction de la **SelectAction** propriété.
- TreeNodeCheckChanged se produit lorsqu’un état checkboxs des nœuds est modifié.
- Événements TreeNodeExpanded se produit lorsqu’un nœud est développé en fonction de la **SelectAction** propriété.
- TreeNodeCollapsed se produit lorsqu’un nœud est réduit.
- TreeNodeDataBound se produit lorsqu’un nœud est de données liée.
- TreeNodePopulate se produit lorsqu’un nœud est rempli.

Le **SelectAction** propriété vous permet de configurer l’événement sont déclenché lorsqu’un nœud est sélectionné. La propriété SelectAction fournit les actions suivantes :

- TreeNodeSelectAction.Expand déclenche événements TreeNodeExpanded lorsque le nœud est sélectionné.
- TreeNodeSelectAction.None ne déclenche aucun événement lorsque le nœud est sélectionné.
- TreeNodeSelectAction.Select déclenche l’événement SelectedNodeChanged lorsque le nœud est sélectionné.
- TreeNodeSelectAction.SelectExpand déclenche l’événement SelectedNodeChanged et l’événement événements TreeNodeExpanded lorsque le nœud est sélectionné.

## <a name="controlling-appearance-with-styles"></a>Contrôle de l’apparence des Styles

Le contrôle TreeView fournit de nombreuses propriétés pour contrôler l’apparence du contrôle avec des styles. Les propriétés suivantes sont disponibles.

| **Nom de propriété** | **Contrôles** |
| --- | --- |
| HoverNodeStyle | Contrôle le style des nœuds lorsque la souris se trouve au-dessus d’eux. |
| LeafNodeStyle | Contrôle le style des nœuds terminaux. |
| NodeStyle | Contrôle le style de tous les nœuds. Les styles de nœud spécifique (par exemple, LeafNodeStyle) remplacent ce style. |
| ParentNodeStyle | Contrôle le style de tous les nœuds parents. |
| RootNodeStyle | Contrôle le style pour le nœud racine. |
| SelectedNodeStyle | Contrôle le style du nœud sélectionné. |

Chacune de ces propriétés est en lecture seule. Toutefois, ils seront chaque retour un **TreeNodeStyle** objet et les propriétés de cet objet peuvent être modifié à l’aide de la *sous-propriétés de la propriété* format. Par exemple, pour définir le **ForeColor** propriété de la **SelectedNodeStyle**, vous utilisez la syntaxe suivante :

[!code-aspx[Main](data-bound-controls/samples/sample8.aspx)]

Notez que la balise ci-dessus n’est pas fermée. Est que lorsque vous utilisez la syntaxe déclarative illustrée ici, vous devez inclure les arborescences de nœuds dans le code HTML.

Les propriétés de style peuvent également être spécifiées dans le code à l’aide du *property.subproperty* format. Par exemple, pour définir le **ForeColor** propriété de la **RootNodeStyle** dans le code, vous utiliseriez la syntaxe suivante :

[!code-csharp[Main](data-bound-controls/samples/sample9.cs)]

> [!NOTE]
> Pour obtenir une liste complète des propriétés de style différent, consultez la documentation MSDN sur l’objet TreeNodeStyle.


## <a name="the-sitemappath-control"></a>Le contrôle SiteMapPath

Le contrôle SiteMapPath fournit un contrôle de navigation de navigation pain pour les développeurs ASP.NET. Comme les autres contrôles de navigation, il peut être facilement lié aux données à des données hiérarchiques, comme le SiteMapDataSource ou XmlDataSource.

Un contrôle SiteMapPath est composé d’objets de SiteMapNodeItem. Il existe trois types de nœuds ; le nœud racine, les nœuds parents et le nœud actuel. Le nœud racine est le nœud en haut de la structure hiérarchique. Le nœud actuel représente la page actuelle. Tous les autres nœuds sont des nœuds parents.

## <a name="controlling-the-operation-of-the-sitemappath-control"></a>Contrôle du fonctionnement du contrôle SiteMapPath

Les propriétés qui contrôlent le fonctionnement du contrôle SiteMapPath sont les suivantes :

| **Property** | **Description de propriété** |
| --- | --- |
| ParentLevelsDisplayed | Contrôle le nombre de nœuds parents est affiché. La valeur par défaut est -1 n’impose aucune restriction sur le nombre de nœuds parents affichés. |
| PathDirection | Contrôle la direction de la SiteMapPath. Les valeurs valides sont RootToCurrent (par défaut) et CurrentToRoot. |
| PathSeparator | Chaîne qui contrôle le caractère qui sépare les nœuds dans un contrôle SiteMapPath. La valeur par défaut est :. |
| RenderCurrentNodeAsLink | Valeur booléenne qui contrôle si le nœud actuel est rendu sous forme de lien. La valeur par défaut est False. |
| SkipLinkText | Aide d’accessibilité lorsque la page est affichée par les lecteurs d’écran. Cette propriété permet d’ignorer le contrôle SiteMapPath lecteurs d’écran. Pour désactiver cette fonctionnalité, définissez la propriété String.Empty. |

## <a name="templated-sitemappath-controls"></a>Contrôles SiteMapPath basé sur un modèle

Le SiteMapControl est un contrôle basé sur un modèle, et par conséquent, vous pouvez définir des modèles différents pour une utilisation dans l’affichage du contrôle. Pour modifier les modèles dans un contrôle SiteMapPath, cliquez sur le bouton de balise active sur le contrôle et choisissez de modifier les modèles à partir du menu. Cela affiche le menu SiteMapTasks comme indiqué ci-dessous dans laquelle vous pouvez choisir entre les différents modèles disponibles.


![](data-bound-controls/_static/image2.jpg)

**Figure 2**


Le **NodeTemplate** modèle fait référence à n’importe quel nœud dans le contrôle SiteMapPath. Si le nœud est un nœud racine ou le nœud actuel et **RootNodeTemplate** ou **CurrentNodeTemplate** est configuré, le NodeTemplate est remplacée.

## <a name="sitemappath-events"></a>Événements SiteMapPath

Le contrôle SiteMapPath a deux événements qui ne sont pas dérivés de la classe du contrôle ; le **ItemCreated** événement et le **ItemDataBound** événement. L’événement ItemCreated est déclenché lorsqu’un élément SiteMapPath est créé. ItemDataBound est déclenché lorsque la méthode DataBind est appelée lors de la liaison de données d’un nœud SiteMapPath. A **SiteMapNodeItemEventArgs** objet fournit l’accès à la SiteMapNodeItem spécifique via la propriété d’élément.

## <a name="controlling-appearance-with-styles"></a>Contrôle de l’apparence des Styles

Les styles suivants sont disponibles pour la mise en forme d’un contrôle SiteMapPath.

| **Nom de propriété** | **Contrôles** |
| --- | --- |
| CurrentNodeStyle | Contrôle le style du texte du nœud actuel. |
| RootNodeStyle | Contrôle le style du texte pour le nœud racine. |
| NodeStyle | Contrôle le style du texte pour tous les nœuds, en supposant que RootNodeStyle de CurrentNodeStyle ne s’applique pas. |

La propriété NodeStyle est remplacée par le CurrentNodeStyle ou le RootNodeStyle. Chacune de ces propriétés est en lecture seule et retourne un **Style** objet. Pour modifier l’apparence d’un nœud à l’aide d’une de ces propriétés, vous devez définir les propriétés de l’objet de Style qui est retourné. Par exemple, le code suivant modifie la propriété de couleur de premier plan du nœud actuel.

[!code-aspx[Main](data-bound-controls/samples/sample10.aspx)]

La propriété peut également être appliquée par programme comme suit :

[!code-csharp[Main](data-bound-controls/samples/sample11.cs)]

> [!NOTE]
> Si un modèle est appliqué, le style n’est pas appliqué.


## <a name="lab-1-configuring-an-aspnet-menu-control"></a>Atelier 1 : Configuration d’un contrôle de Menu ASP.NET

1. Créer un nouveau site Web.
2. Ajouter un fichier de mappage de Site en sélectionnant le fichier, nouveau, fichier, plan de Site à partir de la liste des modèles de fichier.
3. Ouvrez le plan de site (Web.sitemap par défaut) et modifiez-le afin qu’il ressemble à la liste ci-dessous. Les pages à laquelle vous liez dans le fichier de mappage de site n’existent pas réellement, mais qui ne sera pas un problème pour cet exercice.

    [!code-xml[Main](data-bound-controls/samples/sample12.xml)]
4. Ouvrez le formulaire Web par défaut en mode Création.
5. Dans la section de Navigation de la boîte à outils, ajoutez un nouveau contrôle de Menu à la page.
6. Dans la section données de la boîte à outils, ajoutez un nouveau SiteMapDataSource. SiteMapDataSource utilise automatiquement le fichier Web.sitemap dans votre site. (Le fichier Web.sitemap *doit* dans le dossier racine du site.)
7. Cliquez sur le contrôle de Menu, puis sur le bouton de balise active pour afficher la boîte de dialogue de tâches du Menu.
8. Dans la liste déroulante Choisir la Source de données, sélectionnez SiteMapDataSource1.
9. Cliquez sur le lien mise en forme automatique et choisissez un format pour le Menu.
10. Dans le volet Propriétés, définissez la **StaticDisplayLevels** propriété à 2. Le contrôle de Menu doit désormais afficher le nœud d’accueil, produits et Services dans le concepteur.
11. Accédez à la page dans votre navigateur pour utiliser le menu. (Étant donné que les pages que vous avez déjà ajouté au mappage de site n’existent pas vraiment, vous verrez une erreur lorsque vous essayez d’et que vous y accédez.)

Essayer de changer la StaticDisplayLevels et les propriétés MaximumDynamicDisplayLevels et voir comment elles affectent la façon dont le menu est rendu.

## <a name="lab-2-dynamically-binding-a-treeview-control"></a>Atelier 2 : Liaison dynamique d’un contrôle TreeView

Cet exercice part du principe que vous disposez de SQL Server s’exécutant localement et que la base de données Northwind est présent sur l’instance de SQL Server. Si ces conditions ne sont pas remplies, modifiez la chaîne de connexion dans l’exemple. Notez que vous devez également spécifier l’authentification SQL Server au lieu d’une connexion approuvée.

1. Créer un nouveau site Web.
2. Basculez en mode Code pour Default.aspx et remplacez tout le code avec le code ci-dessous. 

    [!code-aspx[Main](data-bound-controls/samples/sample13.aspx)]
3. Enregistrez la page en tant que treeview.aspx.
4. Accédez à la page.
5. Lorsque la page s’affiche tout d’abord, affichez la source de la page dans votre navigateur. Notez que seuls les nœuds visibles ont été envoyés au client.
6. Cliquez sur le signe plus en regard de n’importe quel nœud.
7. Afficher la source dans la page à nouveau. Notez que les nœuds qui vient d’être affichés sont désormais présentes.

## <a name="lab-3-details-view-and-editing-data-using-a-gridview-and-detailsview"></a>Atelier 3 : Détails d’Affichage et modification des données à l’aide d’un contrôle GridView et le contrôle DetailsView

1. Créer un nouveau site Web.
2. Ajouter un nouveau fichier de web.config pour le site Web.
3. Ajouter une chaîne de connexion dans le fichier web.config comme indiqué ci-dessous : 

    [!code-xml[Main](data-bound-controls/samples/sample14.xml)]

    > [!NOTE]
    > Vous devrez peut-être modifier la chaîne de connexion en fonction de votre environnement.
4. Enregistrez et fermez le fichier web.config.
5. Ouvrez Default.aspx et ajouter un nouveau contrôle SqlDataSource.
6. Modifiez l’ID du contrôle SqlDataSource pour **produits**.
7. Dans le **Tâches SqlDataSource** menu, cliquez sur **configurer la Source de données**.
8. Sélectionnez **Northwind** dans la liste déroulante de connexion et cliquez sur Suivant.
9. Sélectionnez **produits** à partir de la **nom** liste déroulante et vérifiez la **ProductID**, **ProductName**, **UnitPrice**, et **UnitsInStock** cases à cocher, comme indiqué ci-dessous. 

![](data-bound-controls/_static/image3.jpg)

    **Figure 3**
10. Cliquez sur **Suivant**.
11. Cliquez sur **Terminer**.
12. Basculez en mode Source et examinez le code qui a été généré. Notez le **SelectCommand**, **DeleteCommand**, **InsertCommand**, et **UpdateCommand** qui ont été ajoutés pour le SqlDataSource contrôle. Notez également les paramètres qui ont été ajoutés.
13. Basculez en mode Design et ajouter un nouveau contrôle GridView à la page.
14. Sélectionnez **produits** à partir de la **choisir la Source de données** liste déroulante.
15. Vérifiez **activer la pagination** et **activer la sélection** comme indiqué ci-dessous. 

![](data-bound-controls/_static/image4.jpg)

    **Figure 4**
16. Cliquez sur le **modifier les colonnes** lien et assurez-vous que **générer automatiquement les champs** est activée.
17. Cliquez sur **OK**.
18. Le contrôle GridView est sélectionné, cliquez sur le bouton en regard du **DataKeyNames** propriété dans le volet Propriétés.
19. Sélectionnez **ProductID** à partir de la **des champs de données disponibles** liste et cliquez sur le  **&gt;**  bouton Ajouter.
20. Cliquez sur OK.
21. Ajouter un nouveau contrôle SqlDataSource à la page.
22. Modifiez l’ID du contrôle SqlDataSource pour **détails**.
23. Dans le menu Tâches SqlDataSource, choisissez **configurer la Source de données**.
24. Choisissez **Northwind** dans la liste déroulante et cliquez sur **suivant**.
25. Sélectionnez **produits** à partir de la **nom** liste déroulante et vérifiez la  **\***  case à cocher dans la **colonnes** listbox.
26. Cliquez sur le **où** bouton.
27. Sélectionnez **ProductID** à partir de la **colonne** liste déroulante.
28. Sélectionnez  **=**  dans la liste déroulante opérateur.
29. Sélectionnez **contrôle** à partir de la **Source** liste déroulante.
30. Sélectionnez **GridView1** à partir de la **ID de contrôle** liste déroulante.
31. Cliquez sur le **ajouter** pour ajouter la clause WHERE.
32. Cliquez sur **OK**.
33. Cliquez sur le **avancé** bouton et vérifiez la **instructions générer INSERT, UPDATE et DELETE** case à cocher.
34. Cliquez sur **OK**.
35. Cliquez sur **suivant** et cliquez sur **Terminer**.
36. Ajouter un contrôle DetailsView à la page.
37. Dans le **choisir la Source de données** liste déroulante, choisissez **détails**.
38. Vérifiez le **activer la modification** case à cocher, comme indiqué ci-dessous. 

![](data-bound-controls/_static/image1.gif)

    **Figure 5**
39. Enregistrez la page et parcourir Default.aspx.
40. Cliquez sur le **sélectionnez** lien en regard de différents enregistrements pour afficher la mise à jour de DetailsView automatiquement.
41. Cliquez sur le **modifier** lien dans le contrôle DetailsView.
42. Apportez une modification à l’enregistrement, puis cliquez sur **mise à jour**.
