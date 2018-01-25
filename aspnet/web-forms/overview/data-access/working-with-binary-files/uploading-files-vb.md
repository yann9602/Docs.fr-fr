---
uid: web-forms/overview/data-access/working-with-binary-files/uploading-files-vb
title: "Téléchargement de fichiers (VB) | Documents Microsoft"
author: rick-anderson
description: "Découvrez comment permettre aux utilisateurs de télécharger les fichiers binaires (tels que des documents Word ou PDF) à votre site Web où ils peuvent être stockés dans le système de fichiers soit du serveur..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/27/2007
ms.topic: article
ms.assetid: f7c00fbd-652c-433d-8ed3-0e5168a4d4df
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/working-with-binary-files/uploading-files-vb
msc.type: authoredcontent
ms.openlocfilehash: 69586ade54a40aabb55dd507731a6c2820774c04
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="uploading-files-vb"></a>Téléchargement de fichiers (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_54_VB.exe) ou [télécharger le PDF](uploading-files-vb/_static/datatutorial54vb1.pdf)

> Découvrez comment permettre aux utilisateurs de télécharger les fichiers binaires (tels que des documents Word ou PDF) à votre site Web où ils peuvent être stockés dans le système de fichiers du serveur ou de la base de données.


## <a name="introduction"></a>Introduction

Tous les didacticiels nous ve examiné jusqu'à présent ont fonctionné exclusivement avec des données de texte. Toutefois, de nombreuses applications ont des modèles de données qui capture les données texte et binaires. Un site rencontre en ligne peut permettre aux utilisateurs de télécharger une image à associer à son profil. Un site Web de recrutement peut permettre aux utilisateurs de télécharger leur reprise sous forme de document Microsoft Word ou PDF.

Utilisation des données binaires ajoute un nouvel ensemble de défis. Nous devons déterminer la façon dont les données binaires sont stockées dans l’application. L’interface utilisée pour insérer de nouveaux enregistrements doit être mis à jour pour autoriser l’utilisateur à télécharger un fichier à partir de leur ordinateur et des étapes supplémentaires doivent être prises pour afficher ou fournir un moyen pour le téléchargement d’un enregistrement s données binaires associées données. Dans ce didacticiel et les trois suivants, nous allons observer comment hurdle ces défis. À la fin de ces didacticiels nous allons avoir créé une application entièrement fonctionnelle qui associe une image et une brochure au format PDF à chaque catégorie. Dans ce didacticiel nous examiner différentes techniques pour le stockage des données binaires et découvrez comment permettre aux utilisateurs de télécharger un fichier à partir de leur ordinateur et l’avez enregistré sur le système de fichiers web server s.

> [!NOTE]
> Les données binaires qui fait partie d’un modèle de données d’application s sont parfois appelées un [BLOB](http://en.wikipedia.org/wiki/Binary_large_object), un acronyme pour ces objets. Dans ces didacticiels, j’ai choisi d’utiliser les données binaires de la terminologie, bien que le terme de l’objet BLOB est synonyme.


## <a name="step-1-creating-the-working-with-binary-data-web-pages"></a>Étape 1 : Création de la plage de travail avec des Pages Web de données binaires

Avant de commencer à Explorer les défis liés à l’ajout de la prise en charge pour les données binaires, permettent de s tout d’abord prendre quelques instants pour créer les pages ASP.NET dans notre projet de site Web que nous aurons besoin pour ce didacticiel et les trois suivants. Commencez par ajouter un nouveau dossier nommé `BinaryData`. Ensuite, ajoutez les pages ASP.NET suivantes à ce dossier, en veillant à associer chaque page avec le `Site.master` page maître :

- `Default.aspx`
- `FileUpload.aspx`
- `DisplayOrDownloadData.aspx`
- `UploadInDetailsView.aspx`
- `UpdatingAndDeleting.aspx`


![Ajouter les Pages ASP.NET pour les didacticiels relatifs aux données binaires](uploading-files-vb/_static/image1.gif)

**Figure 1**: ajouter les Pages ASP.NET pour les didacticiels relatifs aux données binaires


Comme dans les autres dossiers, `Default.aspx` dans le `BinaryData` dossier répertorie les didacticiels dans sa section. N’oubliez pas que le `SectionLevelTutorialListing.ascx` contrôle utilisateur fournit cette fonctionnalité. Par conséquent, ajoutez ce contrôle utilisateur pour `Default.aspx` en le faisant glisser à partir de l’Explorateur de solutions sur la page s mode Création.


[![Ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx](uploading-files-vb/_static/image2.gif)](uploading-files-vb/_static/image1.png)

**Figure 2**: ajouter la `SectionLevelTutorialListing.ascx` contrôle utilisateur à `Default.aspx` ([cliquez pour afficher l’image en taille réelle](uploading-files-vb/_static/image2.png))


Enfin, ajoutez ces pages en tant qu’entrées pour les `Web.sitemap` fichier. En particulier, ajoutez le balisage suivant après l’optimisation du GridView `<siteMapNode>`:


[!code-xml[Main](uploading-files-vb/samples/sample1.xml)]

Après la mise à jour `Web.sitemap`, prenez un moment pour afficher le site Web des didacticiels via un navigateur. Le menu de gauche inclut désormais les éléments pour l’utilisation des didacticiels de données binaires.


![Le plan de Site inclut maintenant des entrées pour la plage de travail dans les didacticiels de données binaires](uploading-files-vb/_static/image3.gif)

**Figure 3**: le plan de Site inclut maintenant des entrées pour la plage de travail dans les didacticiels de données binaires


## <a name="step-2-deciding-where-to-store-the-binary-data"></a>Étape 2 : Choix de l’emplacement stocker les données binaires

Les données binaires qui sont associées avec le modèle de données d’application s peuvent être stockées dans un des deux emplacements : sur le système de fichiers de serveur s web avec une référence au fichier stocké dans la base de données ; ou directement dans la base de données (voir Figure 4). Chaque approche possède son propre ensemble de leurs avantages et inconvénients et mérite une présentation plus détaillée.


[![Données binaires peuvent être stockées sur le système de fichiers ou directement dans la base de données](uploading-files-vb/_static/image4.gif)](uploading-files-vb/_static/image3.png)

**Figure 4**: données binaires peuvent être stockées sur le système de fichiers ou directement dans la base de données ([cliquez pour afficher l’image en taille réelle](uploading-files-vb/_static/image4.png))


Supposons que nous voulons étendre la base de données Northwind pour associer une image de chaque produit. L’une des options serait de stocker ces fichiers image sur le système de fichiers de serveur s web et d’enregistrer le chemin d’accès dans le `Products` table. Avec cette approche, nous d ajouter un `ImagePath` colonne à la `Products` table de type `varchar(200)`, par exemple. Lorsqu’un utilisateur a téléchargé une image de Tran, cette image peut être stockée sur le système de fichiers de serveur s web à `~/Images/Tea.jpg`, où `~` représente le chemin d’accès physique s application. Autrement dit, si le site web racine dans le chemin d’accès physique `C:\Websites\Northwind\`, `~/Images/Tea.jpg` équivaut à `C:\Websites\Northwind\Images\Tea.jpg`. Après avoir téléchargé le fichier image, nous d mettre à jour l’enregistrement de Tran dans le `Products` table afin que ses `ImagePath` colonne référencée le chemin d’accès de la nouvelle image. Nous pouvons utiliser `~/Images/Tea.jpg` ou simplement `Tea.jpg` si nous avons décidé que toutes les images de produit sont placées dans le s application `Images` dossier.

Les principaux avantages du stockage des données binaires sur le système de fichiers sont :

- **Facilité d’implémentation** comme nous le verrons dans quelques instants, le stockage et la récupération des données binaires stockées directement dans la base de données impliquent un peu plus de code que lors de l’utilisation des données dans le système de fichiers. En outre, pour qu’un utilisateur afficher ou télécharger des données binaires qu’ils doivent être présentés avec une URL à ces données. Si les données résident sur le système de fichiers s serveur web, l’URL est simple. Si les données sont stockées dans la base de données, cependant, une page web doit être créée qui Récupère et renvoyer les données à partir de la base de données.
- **Étendre l’accès aux données binaires** les données binaires devront peut-être être accessible à d’autres services ou applications, ceux qui ne peut pas extraire les données à partir de la base de données. Par exemple, les images associées à chaque produit peuvent également devront être disponibles aux utilisateurs via [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol), auquel cas nous d souhaitez stocker les données binaires sur le système de fichiers.
- **Performances** si les données binaires sont stockées sur le système de fichiers, la congestion à la demande et le réseau entre le serveur de base de données et le serveur web est inférieure au si les données binaires sont stockées directement dans la base de données.

Le principal inconvénient du stockage des données binaires sur le système de fichiers est de séparer les données à partir de la base de données. Si un enregistrement est supprimé de la `Products` table, le fichier associé sur le système de fichiers de serveur s web n’est pas supprimée automatiquement. Nous devons écrire du code supplémentaire pour supprimer le fichier ou le système de fichiers sera encombrer des fichiers orphelins inutilisés. En outre, lorsque vous sauvegardez la base de données, nous devons faire qu’effectuer des sauvegardes des données binaires associées sur le système de fichiers. Déplacement de la base de données à un autre site ou serveur pose des défis semblables.

Vous pouvez également les données binaires peuvent être stockées directement dans une base de données Microsoft SQL Server 2005 en créant une colonne de type `varbinary`. Comme avec d’autres types de données de longueur variable, vous pouvez spécifier une longueur maximale des données binaires qui peuvent être contenues dans cette colonne. Par exemple, pour réserver au maximum 5 000 octets utiliser `varbinary(5000)`; `varbinary(MAX)` autorise la taille maximale de stockage, environ 2 Go.

Le principal avantage du stockage des données binaires directement dans la base de données est le couplage étroit entre les données binaires et de l’enregistrement de la base de données. Cela simplifie considérablement les tâches d’administration de base de données, telles que les sauvegardes ou en déplaçant la base de données à un autre site ou un serveur. En outre, la suppression un enregistrement supprime automatiquement les données binaires correspondantes. Il existe également d’autres avantages subtiles de stocker les données binaires dans la base de données. Consultez [stocker le fichiers binaires directement dans la base de données à l’aide de ASP.NET 2.0](http://aspnet.4guysfromrolla.com/articles/120606-1.aspx) pour une explication plus approfondie.

> [!NOTE]
> Dans Microsoft SQL Server 2000 et versions antérieures, le `varbinary` du type de données est une limite maximale de 8 000 octets. Pour stocker jusqu'à 2 Go de données binaires du [ `image` type de données](https://msdn.microsoft.com/library/ms187993.aspx) doit être utilisée à la place. Avec l’ajout de `MAX` dans SQL Server 2005, cependant, la `image` type de données a été déconseillé. Il s toujours prise en charge pour ascendante compatibilité, mais Microsoft a annoncé que la `image` type de données sera supprimé dans une future version de SQL Server.


Si vous travaillez avec un modèle de données plus anciens, vous pouvez voir le `image` type de données. La base de données Northwind s `Categories` table a un `Picture` colonne qui peut être utilisé pour stocker les données binaires d’un fichier image pour la catégorie. Étant donné que la base de données Northwind a ses racines dans Microsoft Access et les versions antérieures de SQL Server, cette colonne est de type `image`.

Pour ce didacticiel et les trois suivants, nous allons utiliser ces deux approches. Le `Categories` table possède déjà un `Picture` colonne pour stocker le contenu binaire d’une image pour la catégorie. Nous allons ajouter une colonne supplémentaire, `BrochurePath`, pour stocker un chemin d’accès au format PDF sur le système de fichiers de serveur s web qui peut être utilisé pour fournir une vue d’ensemble de la qualité d’impression, l’aspect de la catégorie.

## <a name="step-3-adding-thebrochurepathcolumn-to-thecategoriestable"></a>Étape 3 : Ajout de la`BrochurePath`colonne à la`Categories`Table

La table Categories a actuellement que quatre colonnes : `CategoryID`, `CategoryName`, `Description`, et `Picture`. En plus de ces champs, nous devons ajouter un nouveau qui pointe vers la brochure catégorie s (le cas échéant). Pour ajouter cette colonne, accédez à l’Explorateur de serveurs, explorez les Tables, avec le bouton droit sur le `Categories` de table et choisissez Ouvrir la définition de Table (voir Figure 5). Si vous ne voyez pas l’Explorateur de serveurs, mettez-le en sélectionnant l’option de l’Explorateur de serveurs dans le menu Affichage, ou appuyez sur Ctrl + Alt + S.

Ajouter un nouveau `varchar(200)` colonne à la `Categories` table nommée `BrochurePath` ainsi `NULL` s et cliquez sur l’icône Enregistrer (ou appuyez sur Ctrl + S).


[![Ajouter une colonne BrochurePath à la Table de catégories](uploading-files-vb/_static/image5.gif)](uploading-files-vb/_static/image5.png)

**Figure 5**: ajouter un `BrochurePath` colonne à la `Categories` Table ([cliquez pour afficher l’image en taille réelle](uploading-files-vb/_static/image6.png))


## <a name="step-4-updating-the-architecture-to-use-thepictureandbrochurepathcolumns"></a>Étape 4 : Mise à jour de l’Architecture à utiliser le`Picture`et`BrochurePath`colonnes

Le `CategoriesDataTable` dans la couche DAL (Data Access) a actuellement quatre `DataColumn` s définies : `CategoryID`, `CategoryName`, `Description`, et `NumberOfProducts`. Lorsque nous avons conçu à l’origine ce DataTable dans le [création d’une couche d’accès aux données](../introduction/creating-a-data-access-layer-vb.md) didacticiel, les `CategoriesDataTable` avait uniquement les trois premières colonnes ; la `NumberOfProducts` colonne a été ajoutée dans le [maître/détail à l’aide d’une puce Liste d’enregistrements principale avec un contrôle DataList détails](../filtering-scenarios-with-the-datalist-and-repeater/master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb.md) didacticiel.

Comme indiqué dans *création d’une couche d’accès aux données*, les tables de données dans le DataSet typé constituent les objets métier. Les TableAdapters sont responsables de la communication avec la base de données et remplir les objets d’entreprise avec les résultats de requête. Le `CategoriesDataTable` est rempli par le `CategoriesTableAdapter`, qui a trois méthodes de récupération de données :

- `GetCategories()`exécute la requête principale de TableAdapter s et retourne le `CategoryID`, `CategoryName`, et `Description` les champs de tous les enregistrements dans la `Categories` table. La requête principale est celui qui est utilisé par générées automatiquement `Insert` et `Update` méthodes.
- `GetCategoryByCategoryID(categoryID)`Retourne le `CategoryID`, `CategoryName`, et `Description` champs de la catégorie dont `CategoryID` est égal à *categoryID*.
- `GetCategoriesAndNumberOfProducts()`-Retourne la `CategoryID`, `CategoryName`, et `Description` champs pour tous les enregistrements dans la `Categories` table. Utilise également une sous-requête pour retourner le nombre de produits associés à chaque catégorie.

Notez qu’aucune de ces requêtes retournent le `Categories` table s `Picture` ou `BrochurePath` colonnes ; et ne le `CategoriesDataTable` fournir `DataColumn` s pour ces champs. Pour pouvoir travailler avec l’image et `BrochurePath` propriétés, nous devons d’abord les ajouter à la `CategoriesDataTable` puis mettez à jour la `CategoriesTableAdapter` classe pour retourner ces colonnes.

## <a name="adding-thepictureandbrochurepathdatacolumn-s"></a>Ajout de la`Picture`et`BrochurePath``DataColumn` s

Commencez par ajouter ces deux colonnes à la `CategoriesDataTable`. Avec le bouton droit sur le `CategoriesDataTable` en-tête de s, sélectionnez Ajouter dans le menu contextuel et puis choisissez l’option de colonne. Cette opération crée un nouveau `DataColumn` dans le DataTable nommé `Column1`. Renommer cette colonne `Picture`. À partir de la fenêtre Propriétés, définissez la `DataColumn` s `DataType` propriété `System.Byte[]` (cela n’est pas une option dans la liste déroulante, vous devez le saisir dans).


[![Créer une image de nommé DataColumn dont DataType est System.Byte]](uploading-files-vb/_static/image6.gif)](uploading-files-vb/_static/image7.png)

**Figure 6**: créer un `DataColumn` nommé `Picture` dont `DataType` est `System.Byte[]` ([cliquez pour afficher l’image en taille réelle](uploading-files-vb/_static/image8.png))


Ajoutez un autre `DataColumn` dans le DataTable, nommez-la `BrochurePath` à l’aide de la valeur par défaut `DataType` valeur (`System.String`).

## <a name="returning-thepictureandbrochurepathvalues-from-the-tableadapter"></a>Retourner le`Picture`et`BrochurePath`valeurs à partir de TableAdapter

Avec ces deux `DataColumn` s ajouté à la `CategoriesDataTable`, nous re prêt à mettre à jour le `CategoriesTableAdapter`. Nous pourrions avoir tous deux de ces valeurs de colonne retournés dans la requête TableAdapter principale, mais cela serait récupérer les données binaires chaque fois que le `GetCategories()` méthode a été appelée. Au lieu de cela, s permettent de mettre à jour la requête TableAdapter principale pour ramener `BrochurePath` et créer une méthode de récupération de données supplémentaires qui retourne une catégorie particulière s `Picture` colonne.

Pour mettre à jour la requête TableAdapter principale, cliquez sur le `CategoriesTableAdapter` en-tête de s et choisissez l’option de configuration dans le menu contextuel. Ceci fait apparaître l’Assistant Configuration de la carte de la Table, qui nous ve vu dans une série de didacticiels passées. Mettre à jour de la requête pour ramener le `BrochurePath` et cliquez sur Terminer.


[![Mettre à jour la liste des colonnes dans l’instruction SELECT pour renvoyer également BrochurePath](uploading-files-vb/_static/image7.gif)](uploading-files-vb/_static/image9.png)

**Figure 7**: mettre à jour la liste des colonnes dans le `SELECT` instruction pour retourner également `BrochurePath` ([cliquez pour afficher l’image en taille réelle](uploading-files-vb/_static/image10.png))


Lorsque vous utilisez des instructions SQL ad hoc pour le TableAdapter, mise à jour de la liste des colonnes dans la requête principale met à jour la liste des colonnes pour tous les `SELECT` méthodes du TableAdapter de requête. Cela signifie que la `GetCategoryByCategoryID(categoryID)` méthode a été mis à jour pour retourner le `BrochurePath` colonne, qui peut être nous le souhaitions. Toutefois, il également mis à jour la liste des colonnes dans la `GetCategoriesAndNumberOfProducts()` méthode, la suppression de la sous-requête qui renvoie le nombre de produits pour chaque catégorie ! Par conséquent, nous devons mettre à jour de cette méthode s `SELECT` requête. Avec le bouton droit sur le `GetCategoriesAndNumberOfProducts()` méthode, choisissez configurer et rétablir le `SELECT` sa valeur d’origine de la requête :


[!code-sql[Main](uploading-files-vb/samples/sample2.sql)]

Ensuite, créez une nouvelle méthode TableAdapter qui retourne une catégorie particulière s `Picture` valeur de la colonne. Avec le bouton droit sur le `CategoriesTableAdapter` en-tête de s et choisissez l’option Ajouter une requête pour lancer l’Assistant Configuration de requête TableAdapter. La première étape de cet Assistant vous demande de nous que si nous voulons que pour interroger des données à l’aide d’une instruction de SQL ad hoc, une nouvelle procédure stockée, ou il en existe un. Sélectionnez utiliser des instructions SQL et cliquez sur Suivant. Étant donné que nous retourne une ligne, choisissez l’instruction SELECT qui retourne l’option de lignes à partir de la deuxième étape.


[![Sélectionnez l’Option des instructions SQL de l’utilisation](uploading-files-vb/_static/image8.gif)](uploading-files-vb/_static/image11.png)

**Figure 8**: sélectionnez l’Option des instructions SQL de l’utilisation ([cliquez pour afficher l’image en taille réelle](uploading-files-vb/_static/image12.png))


[![Étant donné que la requête renvoie un enregistrement à partir de la Table Categories, choisissez SELECT qui retourne des lignes](uploading-files-vb/_static/image9.gif)](uploading-files-vb/_static/image13.png)

**Figure 9**: étant donné que la requête retourne un enregistrement de la Table de catégories, choisissez l’option Sélectionner qui retourne des lignes ([cliquez pour afficher l’image en taille réelle](uploading-files-vb/_static/image14.png))


Dans la troisième étape, entrez la requête SQL suivante et cliquez sur suivant :


[!code-sql[Main](uploading-files-vb/samples/sample3.sql)]

La dernière étape consiste à choisir le nom de la nouvelle méthode. Utilisez `FillCategoryWithBinaryDataByCategoryID` et `GetCategoryWithBinaryDataByCategoryID` pour le remplissage un DataTable et retourner un DataTable des modèles, respectivement. Cliquez sur Terminer pour terminer l’Assistant.


[![Choisissez les noms pour les méthodes de s TableAdapter](uploading-files-vb/_static/image10.gif)](uploading-files-vb/_static/image15.png)

**La figure 10**: choisissez les noms pour les méthodes du TableAdapter s ([cliquez pour afficher l’image en taille réelle](uploading-files-vb/_static/image16.png))


> [!NOTE]
> Après avoir terminé l’Assistant Configuration de Table adaptateur de requête, vous pouvez voir une boîte de dialogue qui vous informe que le nouveau texte de commande retourne des données avec un schéma différent de celui de la requête principale. En bref, l’Assistant est à noter que la requête principale de TableAdapter s `GetCategories()` retourne un schéma différent de celui que nous venons de créer. Mais il s’agit le résultat escompté, vous pouvez ignorer ce message.


Également, gardez à l’esprit que si vous utilisez les instructions SQL ad hoc et que vous utilisez l’Assistant pour modifier la requête principale de s TableAdapter à un point ultérieur dans le temps, il va modifier le `GetCategoryWithBinaryDataByCategoryID` méthode s `SELECT` liste des colonnes à inclure uniquement les colonnes à partir de l’instruction s le requête principale (autrement dit, elle supprime la `Picture` colonne à partir de la requête). Vous devez mettre à jour manuellement la liste des colonnes pour retourner le `Picture` colonne, similaire à ce que nous l’avons fait avec la `GetCategoriesAndNumberOfProducts()` méthode plus haut dans cette étape.

Après avoir ajouté les deux `DataColumn` s pour le `CategoriesDataTable` et `GetCategoryWithBinaryDataByCategoryID` méthode à la `CategoriesTableAdapter`, ces classes dans le Concepteur de DataSet typé doivent ressembler à la capture d’écran de la Figure 11.


![Le Concepteur de DataSet inclut les nouvelles colonnes et la méthode](uploading-files-vb/_static/image11.gif)

**Figure 11**: le Concepteur de DataSet inclut les nouvelles colonnes et la méthode


## <a name="updating-the-business-logic-layer-bll"></a>Mise à jour de la couche de logique métier (BLL)

Avec la couche DAL mis à jour, il ne reste qu’à augmenter la couche BLL (Business Logic) pour inclure une méthode pour le nouveau `CategoriesTableAdapter` (méthode). Ajoutez la méthode suivante à la `CategoriesBLL` classe :


[!code-vb[Main](uploading-files-vb/samples/sample4.vb)]

## <a name="step-5-uploading-a-file-from-the-client-to-the-web-server"></a>Étape 5 : Téléchargez un fichier à partir du Client au serveur Web

Lors de la collecte des données binaires, ces données sont souvent fournies par un utilisateur final. Pour capturer ces informations, l’utilisateur doit être en mesure de télécharger un fichier à partir de leur ordinateur au serveur web. Les données téléchargées, doivent être intégré au modèle de données, ce qui peut signifier l’enregistrement du fichier au système de fichiers web server s et ajout d’un chemin d’accès au fichier dans la base de données ou l’écriture du contenu binaire directement dans la base de données. Dans cette étape, nous allons examiner comment autoriser un utilisateur à télécharger des fichiers à partir de leur ordinateur au serveur. Dans l’étape suivante du didacticiel, nous allons notre attention à l’intégration du fichier téléchargé avec le modèle de données.

ASP.NET 2.0 s nouvelle [contrôle FileUpload Web](https://msdn.microsoft.com/library/ms227677(VS.80).aspx) fournit un mécanisme permettant aux utilisateurs d’envoyer un fichier à partir de leur ordinateur au serveur web. Le contrôle FileUpload rendu sous la forme un `<input>` élément dont `type` attribut est défini sur le fichier, qui affichent des navigateurs comme une zone de texte avec un bouton Parcourir. En cliquant sur le bouton Parcourir affiche une boîte de dialogue à partir de laquelle l’utilisateur peut sélectionner un fichier. Lorsque le formulaire est publié, le contenu de s de fichier sélectionné est envoyé, ainsi que la publication (postback). Sur le côté serveur, plus d’informations sur le fichier téléchargé sont accessibles via les propriétés de s contrôle FileUpload.

Pour illustrer le téléchargement de fichiers, ouvrez le `FileUpload.aspx` page dans le `BinaryData` dossier, faites glisser un contrôle FileUpload à partir de la boîte à outils vers le concepteur et définir le contrôle s `ID` propriété `UploadTest`. Ensuite, ajoutez un paramètre de contrôle de bouton Web son `ID` et `Text` propriétés `UploadButton` et téléchargez le fichier sélectionné, respectivement. Enfin, placez un contrôle Web Label sous le bouton, effacer son `Text` propriété et définissez son `ID` propriété `UploadDetails`.


[![Ajouter un contrôle FileUpload à la Page ASP.NET](uploading-files-vb/_static/image12.gif)](uploading-files-vb/_static/image17.png)

**Figure 12**: ajouter un contrôle FileUpload à la Page ASP.NET ([cliquez pour afficher l’image en taille réelle](uploading-files-vb/_static/image18.png))


Figure 13 illustre cette page lors de l’affichage via un navigateur. Notez qu’en cliquant sur le bouton Parcourir ouvre une boîte de dialogue de sélection de fichier, qui l’utilisateur de choisir un fichier à partir de leur ordinateur. Une fois qu’un fichier a été sélectionné, en cliquant sur le bouton Télécharger le fichier sélectionné provoque une publication (postback) qui envoie le contenu binaire s de fichier sélectionné sur le serveur web.


[![L’utilisateur peut sélectionner un fichier à télécharger à partir de leur ordinateur au serveur](uploading-files-vb/_static/image13.gif)](uploading-files-vb/_static/image19.png)

**Figure 13**: l’utilisateur peut sélectionner un fichier à télécharger à partir de leur ordinateur au serveur ([cliquez pour afficher l’image en taille réelle](uploading-files-vb/_static/image20.png))


Lors de la publication, le fichier téléchargé peut être enregistré dans le système de fichiers ou des données binaires peuvent être manipulées directement par le biais d’un flux de données. Pour cet exemple, laisser s créer un `~/Brochures` dossier et enregistrez-y le fichier téléchargé. Commencez par ajouter le `Brochures` dossier pour le site en tant que sous-dossier du répertoire racine. Ensuite, créez un gestionnaire d’événements pour le `UploadButton` s `Click` événement et ajoutez le code suivant :


[!code-vb[Main](uploading-files-vb/samples/sample5.vb)]

Le contrôle FileUpload fournit diverses propriétés pour travailler avec les données téléchargées. Par exemple, le [ `HasFile` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.fileupload.hasfile.aspx) indique si un fichier a été téléchargé par l’utilisateur, tandis que la [ `FileBytes` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.fileupload.filebytes.aspx) fournit l’accès aux données binaires téléchargés en tant que tableau d’octets. Le `Click` Gestionnaire d’événements démarre en veillant à ce qu’un fichier a été téléchargé. Si un fichier a été téléchargé, l’étiquette affiche le nom du fichier téléchargé, sa taille en octets et son type de contenu.

> [!NOTE]
> Pour vous assurer que l’utilisateur télécharge un fichier que vous pouvez vérifier le `HasFile` propriété et affiche un avertissement si elle s `False`, ou vous pouvez utiliser la [contrôle RequiredFieldValidator](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/validation/default.aspx) à la place.


Le type FileUpload s `SaveAs(filePath)` enregistre le fichier téléchargé spécifié *filePath*. *filePath* doit être un *chemin d’accès physique* (`C:\Websites\Brochures\SomeFile.pdf`) plutôt qu’un *virtuels* *chemin d’accès* (`/Brochures/SomeFile.pdf`). Le [ `Server.MapPath(virtPath)` méthode](https://msdn.microsoft.com/library/system.web.httpserverutility.mappath.aspx) prend un chemin d’accès virtuel et retourne son chemin d’accès physique correspondant. Ici, le chemin d’accès virtuel est `~/Brochures/fileName`, où *nom de fichier* est le nom du fichier téléchargé. Consultez [à l’aide de Server.MapPath](http://www.4guysfromrolla.com/webtech/121799-1.shtml) pour plus d’informations sur les chemins d’accès virtuels et physiques et l’utilisation `Server.MapPath`.

Après avoir effectué la `Click` Gestionnaire d’événements, prenez un moment pour tester la page dans un navigateur. Cliquez sur le bouton Parcourir et sélectionner un fichier à partir de votre disque dur et puis cliquez sur le bouton Télécharger le fichier sélectionné. La publication (postback) envoie le contenu du fichier sélectionné pour le serveur web, qui affiche des informations sur le fichier avant de l’enregistrer à le `~/Brochures` dossier. Après avoir téléchargé le fichier, revenez à Visual Studio et cliquez sur le bouton Actualiser dans l’Explorateur de solutions. Vous devez voir le fichier que vous venez de télécharger dans le dossier ~/Brochures !


[![Le fichier EvolutionValley.jpg ont été téléchargés vers le serveur Web](uploading-files-vb/_static/image14.gif)](uploading-files-vb/_static/image21.png)

**La figure 14**: le fichier `EvolutionValley.jpg` ont été téléchargés vers le serveur Web ([cliquez pour afficher l’image en taille réelle](uploading-files-vb/_static/image22.png))


![EvolutionValley.jpg a été enregistré dans le dossier ~/Brochures](uploading-files-vb/_static/image15.gif)

**Figure 15**: `EvolutionValley.jpg` a été enregistré dans le `~/Brochures` dossier


## <a name="subtleties-with-saving-uploaded-files-to-the-file-system"></a>Subtilités avec l’enregistrement des fichiers téléchargés dans le système de fichiers

Il existe plusieurs subtilités devant être résolues lors de l’enregistrement de téléchargement des fichiers dans le système de fichiers s serveur web. Tout d’abord, il existe s le problème de sécurité. Pour enregistrer un fichier dans le système de fichiers, le contexte de sécurité sous lequel la page ASP.NET s’exécute doit avoir des autorisations en écriture. Le serveur Web de développement ASP.NET s’exécute sous le contexte de votre compte d’utilisateur actuel. Si vous utilisez s de Microsoft Internet Information Services (IIS) en tant que le serveur web, le contexte de sécurité varie selon la version d’IIS et sa configuration.

Un autre défi de l’enregistrement de fichiers au système de fichiers tourne autour de nommer les fichiers. Actuellement, notre page enregistre tous les fichiers téléchargés à le `~/Brochures` répertoire à l’aide du même nom que le fichier sur l’ordinateur client s. Si l’utilisateur A télécharge une brochure portant le nom `Brochure.pdf`, le fichier sera enregistré sous `~/Brochure/Brochure.pdf`. Mais que se passe-t-il si un certain temps ultérieure utilisateur B télécharge un fichier de brochure différents qui ont le même nom de fichier (`Brochure.pdf`) ? Avec le code, nous avons à présent, utilisateur, un fichier s sera remplacé par le téléchargement de s utilisateur B.

Il existe un certain nombre de techniques pour résoudre les conflits de nom de fichier. Une option consiste à empêcher le téléchargement d’un fichier s’il existe déjà avec le même nom. Avec cette approche, lorsque l’utilisateur B tente de télécharger un fichier nommé `Brochure.pdf`, le système ne serait pas enregistrer leurs fichiers et affiche à la place un message informant l’utilisateur B pour renommer le fichier et réessayez. Une autre approche consiste à enregistrer le fichier à l’aide d’un nom de fichier unique, qui peut être un [identificateur global unique (GUID)](http://en.wikipedia.org/wiki/Globally_Unique_Identifier) ou la valeur de l’enregistrement s de colonnes de clés primaires de base de données (en supposant que le téléchargement est associé à un ligne particulière dans le modèle de données). Dans l’étape suivante du didacticiel, nous allons explorer ces options plus en détail.

## <a name="challenges-involved-with-very-large-amounts-of-binary-data"></a>Défis liés avec très grandes quantités de données binaires

Ces didacticiels supposent que les données binaires capturées seront de taille modeste. Utilisation de très grandes quantités de fichiers de données binaires qui sont plusieurs mégaoctets ou plus introduit de nouveaux défis qui dépassent le cadre de ces didacticiels. Par exemple, par défaut ASP.NET rejette les téléchargements de plus de 4 Mo, bien que cela peut être configuré via la [ `<httpRuntime>` élément](https://msdn.microsoft.com/library/e1f13641.aspx) dans `Web.config`. IIS impose ses propres limitations de taille de téléchargement de fichier, trop. Consultez [taille du fichier de téléchargement de IIS](http://vandamme.typepad.com/development/2005/09/iis_upload_file.html) pour plus d’informations. En outre, le temps nécessaire pour télécharger des fichiers volumineux peut dépasser la valeur par défaut 110 secondes QU'ASP.NET doit attendre une demande. Il existe également des problèmes de mémoire et de performances qui surviennent lorsque vous travaillez avec des fichiers volumineux.

Le contrôle FileUpload n’est pas pratique pour le téléchargement de fichiers volumineux. Comme le contenu du fichier s est envoyé au serveur, l’utilisateur final doit attendre patiemment sans aucune confirmation que leur téléchargement progresse. Cela n’est pas bien un problème lors du traitement des fichiers plus petits qui peuvent être téléchargés en quelques secondes, mais peuvent poser un problème lors du traitement des fichiers plus volumineux qui peuvent prendre les minutes à charger. Il existe une multitude de fichiers tiers à des contrôles de téléchargement sont mieux adapté pour la gestion des téléchargements volumineux et la plupart de ces éditeurs fournissent des indicateurs de progression et ActiveX téléchargement des gestionnaires de proposer une expérience utilisateur beaucoup plus sophistiquée.

Si votre application a besoin gérer les fichiers volumineux, vous devez soigneusement examiner les problèmes et trouver des solutions adaptées à vos besoins.

## <a name="summary"></a>Récapitulatif

Création d’une application qui doit capturer les données binaires introduit un certain nombre de défis. Dans ce didacticiel, nous avons exploré les deux premières : choix de l’emplacement stocker les données binaires et qui autorise un utilisateur à télécharger le contenu binaire à une page web. Sur les trois didacticiels, nous verrons comment associer les données transmises avec un enregistrement dans la base de données, ainsi que comment afficher les données binaires en même temps que ses champs de données de texte.

Bonne programmation !

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [À l’aide des Types de données de valeur élevée](https://msdn.microsoft.com/library/ms178158.aspx)
- [Démarrages rapides du contrôle fileUpload](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/ctrlref/standard/fileupload.aspx)
- [Le contrôle de serveur ASP.NET 2.0 FileUpload](http://www.wrox.com/WileyCDA/Section/id-292158.html)
- [L’inconvénient de téléchargements de fichiers](http://www.aspnetresources.com/articles/dark_side_of_file_uploads.aspx)

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Teresa Murphy et Bernadette Leigh. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](updating-and-deleting-existing-binary-data-cs.md)
[Suivant](displaying-binary-data-in-the-data-web-controls-vb.md)
