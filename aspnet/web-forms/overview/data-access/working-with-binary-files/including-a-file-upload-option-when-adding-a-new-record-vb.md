---
uid: web-forms/overview/data-access/working-with-binary-files/including-a-file-upload-option-when-adding-a-new-record-vb
title: "Options incluant un fichier de téléchargement lors de l’ajout d’un nouvel enregistrement (VB) | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel montre comment créer une interface Web qui permet à l’utilisateur à entrer des données de texte et télécharger les fichiers binaires. Pour illustrer l’options disponibles de t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/27/2007
ms.topic: article
ms.assetid: 5776281d-4637-4d1e-a65b-2621d2cade44
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/working-with-binary-files/including-a-file-upload-option-when-adding-a-new-record-vb
msc.type: authoredcontent
ms.openlocfilehash: 4f49c201c71ca8f98d7e15b29f1df9a6bcd1b12e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="including-a-file-upload-option-when-adding-a-new-record-vb"></a>Y compris une Option de téléchargement de fichier lors de l’ajout d’un nouvel enregistrement (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_56_VB.exe) ou [télécharger le PDF](including-a-file-upload-option-when-adding-a-new-record-vb/_static/datatutorial56vb1.pdf)

> Ce didacticiel montre comment créer une interface Web qui permet à l’utilisateur à entrer des données de texte et télécharger les fichiers binaires. Pour illustrer les options disponibles pour stocker des données binaires, un fichier sera enregistré dans la base de données tandis que l’autre est stocké dans le système de fichiers.


## <a name="introduction"></a>Introduction

Dans les deux didacticiels précédents, nous exploré techniques pour le stockage des données binaires qui sont associées avec le modèle de données d’application s, examiné comment utiliser le contrôle FileUpload pour envoyer des fichiers à partir du client au serveur web et vu comment présenter ces données binaires dans les données W contrôle d’EB. Nous avons encore pour vous aider à associer les données téléchargées avec le modèle de données, bien que.

Dans ce didacticiel, nous allons créer une page web pour ajouter une nouvelle catégorie. En plus des zones de texte pour le nom de catégorie et la description, cette page devrez inclure deux contrôles FileUpload pour la nouvelle image de catégorie s et un pour la brochure. L’image téléchargée sera stocké directement dans le nouvel enregistrement s `Picture` colonne, tandis que la brochure sera enregistrée dans le `~/Brochures` dossier avec le chemin d’accès au fichier enregistré dans le nouvel enregistrement s `BrochurePath` colonne.

Avant de créer cette nouvelle page web, nous devrons mettre à jour de l’architecture. Le `CategoriesTableAdapter` requête principale de s ne récupère pas le `Picture` colonne. Par conséquent, générée automatiquement `Insert` méthode dispose uniquement des entrées pour le `CategoryName`, `Description`, et `BrochurePath` champs. Par conséquent, nous devons créer une méthode supplémentaire dans le TableAdapter qui invite à indiquer les quatre `Categories` champs. La `CategoriesBLL` classe dans la couche de logique métier doit également être mis à jour.

## <a name="step-1-adding-aninsertwithpicturemethod-to-thecategoriestableadapter"></a>Étape 1 : Ajout d’un`InsertWithPicture`méthode à la`CategoriesTableAdapter`

Lorsque nous avons créé la `CategoriesTableAdapter` dans les [création d’une couche d’accès aux données](../introduction/creating-a-data-access-layer-vb.md) didacticiel, nous avons configuré pour générer automatiquement `INSERT`, `UPDATE`, et `DELETE` instructions en fonction de la requête principale. En outre, nous avons demandé d’utiliser l’approche Direct de base de données, ce qui a créé les méthodes TableAdapter `Insert`, `Update`, et `Delete`. Exécutent de ces méthodes générées automatiquement `INSERT`, `UPDATE`, et `DELETE` instructions et, par conséquent, acceptez les paramètres d’entrée basés sur les colonnes retournées par la requête principale. Dans le [téléchargement de fichiers](uploading-files-vb.md) didacticiel, nous avons augmentés la `CategoriesTableAdapter` requête principale de s à utiliser le `BrochurePath` colonne.

Étant donné que la `CategoriesTableAdapter` requête principale de s ne fait pas référence à la `Picture` colonne, nous pouvons ni ajouter un nouvel enregistrement ni mettre à jour un enregistrement existant avec une valeur pour le `Picture` colonne. Pour capturer ces informations, nous pouvons soit créer une nouvelle méthode du TableAdapter est utilisée en particulier pour insérer un enregistrement avec des données binaires ou nous pouvons personnaliser générées automatiquement `INSERT` instruction. Le problème avec la personnalisation générées automatiquement `INSERT` instruction est que nous risque notre personnalisations remplacées par l’Assistant. Par exemple, imaginons que nous avons personnalisé les `INSERT` instruction incluent l’utilisation de la `Picture` colonne. Cela serait de mettre à jour le TableAdapter s `Insert` méthode pour inclure un paramètre d’entrée supplémentaire pour les données binaires de catégorie s image s. Nous avons ensuite créer une méthode dans la couche de logique métier pour utiliser cette méthode de la couche DAL et appeler cette méthode de la couche BLL via la couche de présentation et tous les éléments de travail seraient très. Autrement dit, jusqu'à ce que la prochaine fois que nous avons configuré le TableAdapter via l’Assistant Configuration de TableAdapter. Dès que l’Assistant terminée, notre personnalisations à la `INSERT` instruction serait remplacée, le `Insert` méthode retournerait à sa forme ancien et notre code compilera n’est plus !

> [!NOTE]
> Ce problème est un problème lors de l’utilisation de procédures stockées au lieu d’instructions SQL ad hoc. Un didacticiel futures allez Explorer à l’aide de procédures stockées à la place d’instructions SQL ad hoc dans la couche d’accès aux données.


Pour éviter ce risque casse-tête, plutôt que de personnaliser les instructions SQL générées automatiquement permettre de s plutôt créer une nouvelle méthode du TableAdapter. Cette méthode, nommée `InsertWithPicture`, accepte les valeurs pour le `CategoryName`, `Description`, `BrochurePath`, et `Picture` colonnes et exécuter un `INSERT` instruction qui stocke toutes les quatre valeurs d’un nouvel enregistrement.

Ouvrez le DataSet typé et, dans le concepteur, cliquez sur le `CategoriesTableAdapter` en-tête de s et choisissez Ajouter une requête dans le menu contextuel. Cette opération lance l’Assistant de Configuration de requête TableAdapter, qui commence par vous nous demander comment la requête TableAdapter doit-il accéder à la base de données. Choisissez d’utiliser des instructions SQL et cliquez sur Suivant. L’étape suivante vous invite à entrer pour le type de requête à générer. Dans la mesure où re de création d’une requête pour ajouter un nouvel enregistrement à la `Categories` de table, cliquez sur Insérer et cliquez sur Suivant.


[![Sélectionnez l’Option d’insertion](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image1.gif)](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image1.png)

**Figure 1**: sélectionnez l’Option d’insertion ([cliquez pour afficher l’image en taille réelle](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image2.png))


Nous devons maintenant spécifier le `INSERT` instruction SQL. L’Assistant suggère automatiquement un `INSERT` instruction correspondant à la requête principale de TableAdapter s. Dans ce cas, il s une `INSERT` instruction insère les `CategoryName`, `Description`, et `BrochurePath` valeurs. L’instruction de mise à jour afin que les `Picture` colonne est incluse avec un `@Picture` paramètre, comme suit :


[!code-sql[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample1.sql)]

L’écran final de l’Assistant nous demande de nom de la nouvelle méthode du TableAdapter. Entrez `InsertWithPicture` et cliquez sur Terminer.


[![Nom de la nouvelle InsertWithPicture de TableAdapter (méthode)](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image2.gif)](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image3.png)

**Figure 2**: nom de la nouvelle méthode TableAdapter `InsertWithPicture` ([cliquez pour afficher l’image en taille réelle](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image4.png))


## <a name="step-2-updating-the-business-logic-layer"></a>Étape 2 : Mise à jour de la couche de logique métier

Étant donné que la couche de présentation doit uniquement interagir avec la couche de logique métier au lieu de contourner pour accéder directement à la couche d’accès aux données, nous devons créer une méthode de la couche BLL qui appelle la méthode de la couche DAL que nous venons de créer (`InsertWithPicture`). Pour ce didacticiel, créez une méthode dans le `CategoriesBLL` classe nommée `InsertWithPicture` qui accepte comme entrée trois `String` s et un `Byte` tableau. Le `String` sont des paramètres d’entrée pour le nom de la catégorie s, la description et le chemin d’accès du fichier brochure, tandis que le `Byte` tableau est pour le contenu binaire de l’image de catégorie s. Comme le montre le code suivant, cette méthode de la couche BLL appelle la méthode correspondante de la couche DAL :


[!code-vb[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample2.vb)]

> [!NOTE]
> Assurez-vous que vous avez enregistré le DataSet typé avant d’ajouter le `InsertWithPicture` à la couche BLL (méthode). Étant donné que la `CategoriesTableAdapter` code de la classe est généré automatiquement en fonction du DataSet typé, si vous don t tout d’abord enregistrer les modifications apportées au DataSet typé la `Adapter` propriété a gagné t connaître le `InsertWithPicture` (méthode).


## <a name="step-3-listing-the-existing-categories-and-their-binary-data"></a>Étape 3 : Liste des catégories existantes et leurs données binaires

Dans ce didacticiel, nous allons créer une page qui permet à un utilisateur final ajouter une nouvelle catégorie dans le système, en fournissant une image et la brochure pour la nouvelle catégorie. Dans le [didacticiel précédent](displaying-binary-data-in-the-data-web-controls-vb.md) nous avons utilisé un GridView avec ImageField et TemplateField pour afficher chaque nom de catégorie s, description, image et un lien pour télécharger son brochure. Permettent de s répliquer cette fonctionnalité pour ce didacticiel, la création d’une page qui répertorie toutes les catégories existantes et permet aux nouvelles sauvegardes à créer.

Commencez par ouvrir le `DisplayOrDownload.aspx` page à partir de la `BinaryData` dossier. Accédez à la vue de Source et copiez le GridView et ObjectDataSource s la syntaxe déclarative, en les collant dans le `<asp:Content>` élément `UploadInDetailsView.aspx`. En outre, n’oubliez pas de copier sur le `GenerateBrochureLink` méthode à partir de la classe code-behind de `DisplayOrDownload.aspx` à `UploadInDetailsView.aspx`.


[![Copiez et collez la syntaxe déclarative à partir de DisplayOrDownload.aspx à UploadInDetailsView.aspx](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image3.gif)](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image5.png)

**Figure 3**: copiez et collez la syntaxe déclarative à partir de `DisplayOrDownload.aspx` à `UploadInDetailsView.aspx` ([cliquez pour afficher l’image en taille réelle](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image6.png))


Après avoir copié la syntaxe déclarative et `GenerateBrochureLink` méthode sur le `UploadInDetailsView.aspx` page, affichez la page via un navigateur pour vous assurer que tout a été correctement copiée. Vous devez voir un GridView qui répertorie les huit catégories qui inclut un lien pour télécharger la brochure, ainsi que l’image de catégorie s.


[![Vous devez maintenant voir chaque catégorie, ainsi que ses données binaires](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image4.gif)](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image7.png)

**Figure 4**: vous devez maintenant voir chaque catégorie le long d’avec ses données binaires ([cliquez pour afficher l’image en taille réelle](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image8.png))


## <a name="step-4-configuring-thecategoriesdatasourceto-support-inserting"></a>Étape 4 : Configuration du`CategoriesDataSource`à l’insertion de la prise en charge

Le `CategoriesDataSource` ObjectDataSource utilisé par le `Categories` actuellement GridView ne fournit pas la possibilité d’insérer des données. Pour prendre en charge d’insertion de ce contrôle de source de données, nous devons mapper ses `Insert` méthode vers une méthode de l’objet sous-jacent, `CategoriesBLL`. En particulier, nous voulons mapper à la `CategoriesBLL` méthode que nous avons ajouté dans l’étape 2, `InsertWithPicture`.

Démarrez en cliquant sur le lien configurer la Source de données à partir de la balise active de ObjectDataSource s. Le premier écran affiche l’objet de la source de données est configurée pour fonctionner avec, `CategoriesBLL`. Laissez ce paramètre-cliquez sur Suivant pour passer à l’écran de définir des méthodes de données. Déplacer vers l’onglet Insérer et choisir la `InsertWithPicture` méthode dans la liste déroulante. Cliquez sur Terminer pour terminer l’Assistant.


[![Configurer pour utiliser la méthode InsertWithPicture ObjectDataSource](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image5.gif)](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image9.png)

**Figure 5**: configurer l’ObjectDataSource à utiliser le `InsertWithPicture` (méthode) ([cliquez pour afficher l’image en taille réelle](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image10.png))


> [!NOTE]
> À la fin de l’Assistant, Visual Studio peut vous demander si vous souhaitez actualiser les champs et les clés, qui va être régénérée Web les données de contrôle des champs. Choisissez non, car vous sélectionnez Oui pour remplacer toutes les personnalisations de champ que vous avez apportées.


Après la fin de l’Assistant, ObjectDataSource comprend maintenant une valeur pour son `InsertMethod` propriété ainsi que `InsertParameters` pour les colonnes de la catégorie de quatre, par le balisage déclaratif suivant montre :


[!code-aspx[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample3.aspx)]

## <a name="step-5-creating-the-inserting-interface"></a>Étape 5 : Création de l’Interface de l’insertion

Comme tout d’abord traitées dans le [une vue d’ensemble d’insertion, de mise à jour et de suppression des données](../editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-vb.md), le contrôle DetailsView fournit une interface d’insertion intégrée qui peut être utilisée lorsque vous travaillez avec un contrôle de source de données qui prend en charge l’insertion. Permettent d’ajouter un contrôle DetailsView à cette page au-dessus du GridView qui restituera définitivement son interface insertion, permettant à un utilisateur d’ajouter rapidement une nouvelle catégorie s. Lors de l’ajout d’une nouvelle catégorie dans le contrôle DetailsView, le contrôle GridView situé sous celui-ci actualiser automatiquement et afficher la nouvelle catégorie.

Démarrage en faisant glisser un contrôle DetailsView à partir de la boîte à outils vers le concepteur ci-dessus le GridView, en définissant son `ID` propriété `NewCategory` et effacement du `Height` et `Width` des valeurs de propriété. À partir de la balise active de s DetailsView, liez-le à l’objet existant `CategoriesDataSource` puis cochez la case à cocher Activer l’insertion.


[![Lier le contrôle DetailsView le CategoriesDataSource et activer l’insertion](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image6.gif)](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image11.png)

**Figure 6**: lier le contrôle DetailsView à la `CategoriesDataSource` et activer l’insertion ([cliquez pour afficher l’image en taille réelle](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image12.png))


Pour définitivement restituer le contrôle DetailsView dans son interface insertion, définissez son `DefaultMode` propriété `Insert`.

Notez que le contrôle DetailsView a cinq BoundFields `CategoryID`, `CategoryName`, `Description`, `NumberOfProducts`, et `BrochurePath` bien que le `CategoryID` BoundField n’est pas rendu dans l’interface d’insertion, car son `InsertVisible` propriété est définie sur `False`. Ces BoundFields existe, car ils sont les colonnes retournées par la `GetCategories()` méthode, c’est ce que ObjectDataSource appelle pour récupérer ses données. Pour insérer, toutefois, nous n t et souhaitez vous permettent de spécifier une valeur pour `NumberOfProducts`. En outre, nous devons les autoriser à télécharger une image pour la nouvelle catégorie ainsi que de télécharger un fichier PDF de brochure.

Supprimer le `NumberOfProducts` BoundField à partir du DetailsView totalement et la mise à jour la `HeaderText` propriétés de la `CategoryName` et `BrochurePath` BoundFields à la catégorie et Brochure, respectivement. Ensuite, convertissez le `BrochurePath` BoundField en TemplateField et ajouter un nouveau TemplateField pour l’image, en donnant à cette nouvelle TemplateField un `HeaderText` valeur de l’image. Déplacer le `Picture` TemplateField afin qu’il soit entre le `BrochurePath` TemplateField et CommandField.


![Lier le contrôle DetailsView le CategoriesDataSource et activer l’insertion](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image7.gif)

**Figure 7**: lier le contrôle DetailsView à la `CategoriesDataSource` et activer l’insertion


Si vous avez converti le `BrochurePath` le TemplateField de BoundField en TemplateField via la boîte de dialogue Modifier les champs, inclut un `ItemTemplate`, `EditItemTemplate`, et `InsertItemTemplate`. Uniquement les `InsertItemTemplate` est nécessaire, toutefois, par conséquent, n’hésitez pas à supprimer les deux autres modèles. À ce stade, votre syntaxe déclarative de DetailsView s doit se présenter comme suit :


[!code-aspx[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample4.aspx)]

## <a name="adding-fileupload-controls-for-the-brochure-and-picture-fields"></a>Ajout de contrôles FileUpload pour les brochures et les champs d’image

Actuellement, le `BrochurePath` TemplateField s `InsertItemTemplate` contient une zone de texte, tandis que le `Picture` TemplateField ne contient-elle pas tous les modèles. Nous devons mettre à jour de ces deux s TemplateField `InsertItemTemplate` pour utiliser des contrôles FileUpload.

Dans la balise active de s DetailsView, choisissez l’option Modifier les modèles, puis sélectionnez le `BrochurePath` TemplateField s `InsertItemTemplate` dans la liste déroulante. Supprimer la zone de texte et faites glisser un contrôle FileUpload à partir de la boîte à outils, dans le modèle. Définir le contrôle FileUpload s `ID` à `BrochureUpload`. De même, ajoutez un contrôle FileUpload à la `Picture` TemplateField s `InsertItemTemplate`. Définissez ce contrôle FileUpload s `ID` à `PictureUpload`.


[![Ajouter un contrôle FileUpload à l’élément InsertItemTemplate](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image8.gif)](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image13.png)

**Figure 8**: ajouter un contrôle FileUpload à la `InsertItemTemplate` ([cliquez pour afficher l’image en taille réelle](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image14.png))


Après avoir apporté ces ajouts, la syntaxe déclarative de deux TemplateField s sera :


[!code-aspx[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample5.aspx)]

Lorsqu’un utilisateur ajoute une nouvelle catégorie, vous souhaitez vous assurer que les brochures et image sont du type de fichier correct. Pour la brochure, l’utilisateur doit fournir un fichier PDF. Pour l’image, nous devons l’utilisateur de télécharger un fichier image, mais nous autoriser *tout* de l’image fichier ou uniquement les fichiers d’image d’un type particulier, tel que GIF ou jpg ? Afin de permettre les différents types de fichiers, d nous devons étendre la `Categories` pour y inclure une colonne qui indique le type de fichier afin que ce type peut être envoyé au client via `Response.ContentType` dans `DisplayCategoryPicture.aspx`. Étant donné que nous ne pas avoir une telle colonne, il serait préférable de restreindre les utilisateurs à uniquement en fournissant un type de fichier image spécifique. Le `Categories` images existantes de la table s sont des bitmaps, mais jpg est un format de fichier le plus approprié pour les images pris en charge sur le web.

Si un utilisateur télécharge un type de fichier incorrect, nous devons annuler l’insertion et d’afficher un message indiquant le problème. Ajouter un contrôle Web Label sous le contrôle DetailsView. Définir son `ID` propriété `UploadWarning`, désactivez les son `Text` propriété, la valeur la `CssClass` propriété à avertissement et le `Visible` et `EnableViewState` propriétés à `False`. Le `Warning` classe CSS est définie dans `Styles.css` et restitue le texte dans une police de grande taille, rouge, italique, gras.

> [!NOTE]
> Dans l’idéal, le `CategoryName` et `Description` BoundFields est convertie en TemplateField et leurs interfaces insertion personnalisées. Le `Description` insertion d’interface, par exemple, serait probablement être mieux adapté à une zone de texte multiligne. Et puisque le `CategoryName` colonne n’accepte pas les `NULL` valeurs, un contrôle RequiredFieldValidator doit être ajouté pour vous assurer de l’utilisateur fournit une valeur pour le nouveau nom de catégorie s. Ces étapes sont conservées comme un exercice pour le lecteur. Faire référence à [personnalisation de l’Interface de Modification de données](../editing-inserting-and-deleting-data/customizing-the-data-modification-interface-vb.md) pour des explications approfondies sur ce qui étend les interfaces de modification de données.


## <a name="step-6-saving-the-uploaded-brochure-to-the-web-server-s-file-system"></a>Étape 6 : L’enregistrement de la Brochure téléchargée dans le système de fichiers Web Server s

Lorsque l’utilisateur entre les valeurs pour une nouvelle catégorie et clique sur le bouton d’insertion, une publication (postback) se produit et le flux de travail insertion se déroule. Tout d’abord, le contrôle DetailsView s [ `ItemInserting` événement](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.detailsview.iteminserting.aspx) se déclenche. Ensuite, le s ObjectDataSource `Insert()` méthode est appelée, ce qui aboutit à un nouvel enregistrement est ajouté à la `Categories` table. Après cela, le contrôle DetailsView s [ `ItemInserted` événement](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.detailsview.iteminserted.aspx) se déclenche.

Avant du s ObjectDataSource `Insert()` méthode est appelée, nous devons d’abord vous assurer que les types de fichiers appropriées ont été téléchargés par l’utilisateur et puis enregistrez la brochure PDF dans le système de fichiers de serveur s web. Créer un gestionnaire d’événements pour le contrôle DetailsView s `ItemInserting` événement et ajoutez le code suivant :


[!code-vb[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample6.vb)]

Le Gestionnaire d’événements démarre en référençant la `BrochureUpload` contrôle FileUpload à partir des modèles s DetailsView. Ensuite, si une brochure a été téléchargée, l’extension de s du fichier téléchargé est examinée. Si l’extension n’est pas. PDF, puis un message d’avertissement s’affiche, l’insertion est annulée et l’exécution du Gestionnaire d’événements se termine.

> [!NOTE]
> Partie de confiance sur l’extension de s du fichier téléchargé n’est pas une technique sûr pour garantir que le fichier téléchargé est un document PDF. L’utilisateur peut avoir un document PDF valide avec l’extension `.Brochure`, ou pourrait ont pris un document non PDF et donné un `.pdf` extension. Le contenu binaire du fichier s doit être examinée par programme afin de conclure le plus le type de fichier. Ces approches approfondies, cependant, sont souvent excessifs ; la vérification de l’extension est suffisante pour la plupart des scénarios.


Comme indiqué dans le [téléchargement de fichiers](uploading-files-vb.md) didacticiel, soyez prudent lors de l’enregistrement de fichiers au système de fichiers pour ce téléchargement d’un seul utilisateur s ne sont pas remplacés s d’un autre. Pour ce didacticiel, nous va tenter d’utiliser le même nom que le fichier téléchargé. S’il en existe déjà un fichier dans le `~/Brochures` répertoire portant ce même nom de fichier, toutefois, nous allons ajouter un nombre à la fin jusqu'à ce qu’un nom unique est trouvé. Par exemple, si l’utilisateur télécharge un fichier brochure nommé `Meats.pdf`, mais il existe déjà un fichier nommé `Meats.pdf` dans les `~/Brochures` dossier, nous allons modifier le nom de fichier enregistré pour `Meats-1.pdf`. Si qui existe, nous essaierons de `Meats-2.pdf`, et ainsi de suite jusqu'à ce qu’un nom de fichier unique est trouvé.

Le code suivant utilise la [ `File.Exists(path)` méthode](https://msdn.microsoft.com/en-us/library/system.io.file.exists.aspx) pour déterminer si un fichier existe déjà avec le nom de fichier spécifié. Dans ce cas, il continue d’essayer des nouveaux noms de fichiers de brochure jusqu'à ce qu’aucun conflit n’est trouvée.


[!code-vb[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample7.vb)]

Une fois qu’un nom de fichier valide a été trouvé, le fichier doit être enregistré dans le système de fichiers et les opérations de mappage ObjectDataSource `brochurePath``InsertParameter` valeur doit être mis à jour afin que ce nom de fichier est écrit dans la base de données. Comme nous l’avons vu dans la *téléchargement de fichiers* (didacticiel), le fichier peut être enregistré à l’aide du contrôle FileUpload s `SaveAs(path)` (méthode). Pour mettre à jour le s ObjectDataSource `brochurePath` paramètre, utilisez le `e.Values` collection.


[!code-vb[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample8.vb)]

## <a name="step-7-saving-the-uploaded-picture-to-the-database"></a>Étape 7 : L’enregistrement de l’image téléchargée dans la base de données

Pour stocker l’image téléchargée dans le nouveau `Categories` enregistrements, nous avons besoin affecter le contenu binaire téléchargé au s ObjectDataSource `picture` paramètre dans le DetailsView s `ItemInserting` événement. Toutefois, avant de devoir effectuer, nous devons d’abord vous assurer que l’image téléchargée est JPG et pas un autre type d’image. Dans l’étape 6, permettent d’utiliser l’extension de fichier image téléchargé s afin de déterminer son type s.

Alors que le `Categories` table permet `NULL` les valeurs pour la `Picture` colonne, toutes les catégories actuellement aient une image. Permettent de s forcer l’utilisateur à fournir une image lors de l’ajout d’une nouvelle catégorie via cette page. Le code suivant vérifie qu’une image a été téléchargée et qu’il possède une extension appropriée.


[!code-vb[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample9.vb)]

Ce code doit être placé *avant* le code de l’étape 6 afin que s’il existe un problème avec le téléchargement de l’image, le Gestionnaire d’événements va se terminer avant que le fichier brochure est enregistré dans le système de fichiers.

En supposant qu’un fichier approprié a été téléchargé, affecter le contenu binaire téléchargé à la valeur du paramètre s image avec la ligne de code suivante :


[!code-vb[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample10.vb)]

## <a name="the-completeiteminsertingevent-handler"></a>Le texte complet`ItemInserting`Gestionnaire d’événements

Par souci d’exhaustivité, voici les `ItemInserting` Gestionnaire d’événements dans son intégralité :


[!code-vb[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample11.vb)]

## <a name="step-8-fixing-thedisplaycategorypictureaspxpage"></a>Étape 8 : Corrigé le`DisplayCategoryPicture.aspx`Page

S permettent de prendre quelques instants pour tester l’interface d’insertion et `ItemInserting` Gestionnaire d’événements qui a été créé au cours des quelques étapes. Visitez le `UploadInDetailsView.aspx` page via un navigateur et de la tentative d’ajout d’une collection, mais omettez l’image, ou spécifiez une image non-JPG ou une brochure non PDF. Dans tous ces cas, un message d’erreur s’affiche et le flux de travail d’insertion est annulée.


[![Un Message d’avertissement est affiché si un Type de fichier non valide est téléchargé](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image9.gif)](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image15.png)

**Figure 9**: un Message d’avertissement est affiché si un Type de fichier non valide est téléchargé ([cliquez pour afficher l’image en taille réelle](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image16.png))


Après avoir vérifié que la page requiert une image à télécharger que t gagné / acceptent les fichiers non PDF non-JPG, ajouter une nouvelle catégorie avec une image JPG valide, si vous laissez le champ Brochure vide. Après avoir cliqué sur le bouton d’insertion, la page est publiée et un nouvel enregistrement sera ajouté à la `Categories` table avec le contenu binaire de l’image téléchargée s stockées directement dans la base de données. Le contrôle GridView est mis à jour et affiche une ligne pour la catégorie qui vient d’être ajoutée, mais, comme le montre la Figure 10, la nouvelle image de s catégorie n’est pas rendue correctement.


[![La nouvelle catégorie s Qu'image n’est pas affichée.](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image10.gif)](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image17.png)

**La figure 10**: s de la nouvelle catégorie image n’est pas affiché ([cliquez pour afficher l’image en taille réelle](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image18.png))


La raison pour laquelle la nouvelle image n’est pas affichée est, car le `DisplayCategoryPicture.aspx` page qui retourne une image de la catégorie spécifiée s est configurée pour traiter les bitmaps qui ont un en-tête OLE. Cet en-tête 78 octets est supprimé de la `Picture` contenu binaire de la colonne s avant leur envoi au client. Mais le fichier JPG que simplement chargé pour la nouvelle catégorie n’a pas de cet en-tête OLE ; Par conséquent, les octets valides, nécessaires sont supprimés à partir des données binaires image s.

Dans la mesure où il existe désormais deux bitmaps avec en-têtes OLE et jpg dans le `Categories` table, nous devons mettre à jour `DisplayCategoryPicture.aspx` pour qu’il ne l’en-tête OLE de suppression pour les catégories de huit d’origine et que vous ignore cette suppression pour les enregistrements de catégorie plus récente. Dans notre étape suivante du didacticiel, nous allons examiner comment mettre à jour une image d’enregistrement s existante, et nous allons mettre à jour toutes les images de catégorie ancien afin qu’ils soient jpg. Pour l’instant, cependant, utilisez le code suivant dans `DisplayCategoryPicture.aspx` pour supprimer les en-têtes OLE uniquement pour ces huit catégories d’origine :


[!code-vb[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample12.vb)]

Avec cette modification, l’image JPG est désormais rendu correctement dans le GridView.


[![Les Images JPG de nouvelles catégories sont correctement rendus](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image11.gif)](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image19.png)

**Figure 11**: les Images JPG de nouvelles catégories sont rendus correctement ([cliquez pour afficher l’image en taille réelle](including-a-file-upload-option-when-adding-a-new-record-vb/_static/image20.png))


## <a name="step-9-deleting-the-brochure-in-the-face-of-an-exception"></a>Étape 9 : Suppression de la Brochure en cas de défaillance d’Exception

Un des défis de stocker des données binaires sur le système de fichiers web server s est qu’elle introduit une déconnexion entre le modèle de données et ses données binaires. Par conséquent, chaque fois qu’un enregistrement est supprimé, les données binaires correspondantes sur le système de fichiers doivent également être supprimées. Cela peut entrent en jeu lors de l’insertion, également. Considérez le scénario suivant : un utilisateur ajoute une nouvelle catégorie, en spécifiant une image valide et une brochure. En cliquant sur le bouton Insérer, d’une publication (postback) se produit et le contrôle DetailsView s `ItemInserting` se déclenche l’événement, l’enregistrement de la brochure dans le système de fichiers de serveur s web. Ensuite, le s ObjectDataSource `Insert()` méthode est appelée, qui appelle la `CategoriesBLL` classe s `InsertWithPicture` (méthode), qui appelle la `CategoriesTableAdapter` s `InsertWithPicture` (méthode).

Maintenant, que se passe-t-il si la base de données est hors connexion, ou s’il existe une erreur dans la `INSERT` instruction SQL ? Clairement l’insertion échoue, donc aucune nouvelle ligne de catégorie n’est ajoutée à la base de données. Mais nous avons toujours le fichier téléchargé brochure posé sur le système de fichiers web server s ! Ce fichier doit être supprimé en cas d’une exception pendant l’insertion du flux de travail.

Comme indiqué précédemment dans le [BLL - gestion et des Exceptions au niveau de la couche DAL dans une Page ASP.NET](../editing-inserting-and-deleting-data/handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb.md) (didacticiel), lorsqu’une exception est levée à partir de la profondeur de l’architecture, il est propagée dans à travers les différentes couches. Dans la couche présentation, nous pouvons déterminer si une exception s’est produite à partir de la s DetailsView `ItemInserted` événement. Ce gestionnaire d’événements fournit également les valeurs de mappage ObjectDataSource `InsertParameters`. Par conséquent, nous pouvons créer un gestionnaire d’événements pour le `ItemInserted` événements vérifie si une exception s’est produite et, dans ce cas, supprime le fichier spécifié par le s ObjectDataSource `brochurePath` paramètre :


[!code-vb[Main](including-a-file-upload-option-when-adding-a-new-record-vb/samples/sample13.vb)]

## <a name="summary"></a>Résumé

Il existe un nombre d’étapes qui doivent être effectuées pour fournir une interface basée sur le web pour ajouter des enregistrements qui incluent des données binaires. Si les données binaires sont stockées directement dans la base de données, sans doute que vous devez mettre à jour de l’architecture, ajout de méthodes spécifiques pour gérer le cas où les données binaires sont insérées. Une fois que l’architecture a été mis à jour, l’étape suivante crée l’interface d’insertion, ce qui peut être effectué à l’aide d’un contrôle DetailsView qui a été personnalisé afin d’inclure un contrôle FileUpload pour chaque champ de données binaires. Les données transmises peuvent être enregistrées dans le système de fichiers de serveur s web ou assignées à un paramètre de source de données dans le contrôle DetailsView s `ItemInserting` Gestionnaire d’événements.

L’enregistrement des données binaires dans le système de fichiers requiert plus de planification que l’enregistrement des données directement dans la base de données. Un schéma d’affectation de noms doit être choisi afin d’éviter un seul téléchargement utilisateur s remplaçant s d’un autre. En outre, des étapes supplémentaires requises pour supprimer le fichier téléchargé si l’insertion de la base de données échoue.

Nous disposons désormais la possibilité d’ajouter de nouvelles catégories dans le système avec une brochure et image, mais nous ve restant à examiner comment mettre à jour de la catégorie s binaire données existantes ou supprimer correctement les données binaires pour une catégorie supprimée. Nous étudierons ces deux rubriques dans l’étape suivante du didacticiel.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Dave Gardner Teresa Murphy et Bernadette Leigh. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](displaying-binary-data-in-the-data-web-controls-vb.md)
[Suivant](updating-and-deleting-existing-binary-data-vb.md)
