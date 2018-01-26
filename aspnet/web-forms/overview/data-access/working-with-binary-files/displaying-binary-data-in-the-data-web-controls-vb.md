---
uid: web-forms/overview/data-access/working-with-binary-files/displaying-binary-data-in-the-data-web-controls-vb
title: "Affichage des données binaires dans le site Web de données de contrôle (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons aborder les options de présenter des données binaires sur une page Web, y compris l’affichage d’un fichier image et la fourniture d’un lien 'Télécharger' f..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/27/2007
ms.topic: article
ms.assetid: 9201656a-e1c2-4020-824b-18fb632d2925
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/working-with-binary-files/displaying-binary-data-in-the-data-web-controls-vb
msc.type: authoredcontent
ms.openlocfilehash: df79748bf5734ffcb9eb81ca089aeded0e63bdc5
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="displaying-binary-data-in-the-data-web-controls-vb"></a>Affichage des données binaires dans les contrôles Web de données (Visual Basic)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_55_VB.exe) ou [télécharger le PDF](displaying-binary-data-in-the-data-web-controls-vb/_static/datatutorial55vb1.pdf)

> Dans ce didacticiel, nous examiner les options de présenter des données binaires sur une page Web, y compris l’affichage d’un fichier image et la fourniture d’un lien de « Téléchargement » d’un fichier PDF.


## <a name="introduction"></a>Introduction

Dans le didacticiel précédent, nous exploré les deux techniques permettant d’associer des données binaires avec un modèle de données d’application s sous-jacente et le contrôle FileUpload permet de télécharger des fichiers à partir d’un navigateur pour le système de fichiers s serveur web. Nous avons encore pour savoir comment associer les données binaires téléchargées le modèle de données. Autrement dit, une fois un fichier a été téléchargé et enregistré dans le système de fichiers, un chemin d’accès au fichier doit figurer dans l’enregistrement de la base de données appropriée. Si les données sont stockées directement dans la base de données, les données binaires téléchargées ne doivent pas être enregistrées dans le système de fichiers, mais il doivent être introduites dans la base de données.

Avant d’examiner associant les données du modèle de données, cependant, vous permettent de s d’abord examiner comment fournir les données binaires à l’utilisateur final. Présentation des données de texte est assez simple, mais comment les données binaires doivent être présentées ? Cela dépend bien sûr, sur le type de données binaires. Pour les images, probablement que nous souhaitons afficher l’image. pour les fichiers PDF, les documents Microsoft Word, les fichiers ZIP et les autres types de données binaires, en fournissant un lien de téléchargement est probablement le plus approprié.

Dans ce didacticiel, que nous allons examiner comment présenter les données binaires en même temps que ses données de texte associé à l’aide des données de contrôles Web tels que GridView et DetailsView. Dans l’étape suivante du didacticiel, nous allons notre attention sur l’association d’un fichier téléchargé à la base de données.

## <a name="step-1-providingbrochurepathvalues"></a>Étape 1 : Fournissant`BrochurePath`valeurs

Le `Picture` colonne dans la `Categories` table contient déjà des données binaires pour les images de catégories différentes. Plus précisément, la `Picture` colonne pour chaque enregistrement contient le contenu binaire d’une image bitmap grenue basse qualité, 16 couleurs. Image de chaque catégorie est 172 pixels de larges et 120 pixels de hauteur et consomme environ 11 Ko. Nouveautés plus, le contenu binaire dans le `Picture` colonne inclut un octet de 78 [OLE](http://en.wikipedia.org/wiki/Object_Linking_and_Embedding) en-tête doit être supprimé avant l’affichage de l’image. Ces informations d’en-tête sont présentes, car la base de données Northwind a ses racines dans Microsoft Access. Dans Access, les données binaires sont stockées à l’aide du type de données OLE Object, qui s’attache à cet en-tête. Pour l’instant, nous verrons comment supprimer les en-têtes à partir de ces images de basse qualité afin d’afficher l’image. Dans un didacticiel futur, nous allons construire une interface pour la mise à jour une catégorie s `Picture` colonne et remplacer ces images bitmap qui utilisent des en-têtes OLE avec des images JPG équivalents sans les en-têtes OLE inutiles.

Dans le didacticiel précédent, nous avons vu comment utiliser le contrôle FileUpload. Par conséquent, vous pouvez commencer et ajouter des fichiers de brochure au système de fichiers serveur s web. Ce faisant, toutefois, ne pas mettre à jour le `BrochurePath` colonne dans la `Categories` table. Dans l’étape suivante du didacticiel, nous verrons comment accomplir cette tâche, mais pour l’instant, nous devons fournir manuellement des valeurs pour cette colonne.

Dans ce téléchargement didacticiel s, vous trouverez les fichiers PDF brochure sept dans le `~/Brochures` dossier, un pour chacune de ces catégories, à l’exception Seafood. J’ai volontairement omis d’ajout d’une brochure Seafood pour illustrer comment gérer des scénarios où pas tous les enregistrements ayant binaire des données associées. Pour mettre à jour le `Categories` table avec ces valeurs, avec le bouton droit sur le `Categories` nœud à partir de l’Explorateur de serveurs et choisissez Afficher les données de Table. Ensuite, entrez les chemins d’accès virtuels pour les fichiers brochure pour chaque catégorie qui a une brochure, comme la Figure 1 montre. Dans la mesure où il n’est aucun trois volets pour la catégorie Seafood, laissez son `BrochurePath` valeur de colonne s `NULL`.


[![Entrez manuellement les valeurs pour la colonne BrochurePath catégories s](displaying-binary-data-in-the-data-web-controls-vb/_static/image1.gif)](displaying-binary-data-in-the-data-web-controls-vb/_static/image1.png)

**Figure 1**: entrez manuellement les valeurs pour le `Categories` Table s `BrochurePath` colonne ([cliquez pour afficher l’image en taille réelle](displaying-binary-data-in-the-data-web-controls-vb/_static/image2.png))


## <a name="step-2-providing-a-download-link-for-the-brochures-in-a-gridview"></a>Étape 2 : Fournir un lien de téléchargement pour les Brochures dans un GridView

Avec la `BrochurePath` valeurs fournies pour le `Categories` table, nous re prêt à créer un GridView qui répertorie chaque catégorie avec un lien pour télécharger la brochure catégorie s. À l’étape 4, nous allons étendre cette GridView pour afficher également l’image de catégorie s.

Commencez par faire glisser un contrôle GridView à partir de la boîte à outils vers le Concepteur de la `DisplayOrDownloadData.aspx` page dans le `BinaryData` dossier. Définir le contrôle GridView s `ID` à `Categories` et via la balise active de s GridView, choisissez de lier à une source de données. Plus précisément, la lier à un ObjectDataSource nommé `CategoriesDataSource` qui extrait des données à l’aide du `CategoriesBLL` objet s `GetCategories()` (méthode).


[![Créer un nouveau ObjectDataSource nommé CategoriesDataSource](displaying-binary-data-in-the-data-web-controls-vb/_static/image2.gif)](displaying-binary-data-in-the-data-web-controls-vb/_static/image3.png)

**Figure 2**: créer un nouveau nommé de ObjectDataSource `CategoriesDataSource` ([cliquez pour afficher l’image en taille réelle](displaying-binary-data-in-the-data-web-controls-vb/_static/image4.png))


[![Configurer pour utiliser la classe CategoriesBLL ObjectDataSource](displaying-binary-data-in-the-data-web-controls-vb/_static/image3.gif)](displaying-binary-data-in-the-data-web-controls-vb/_static/image5.png)

**Figure 3**: configurer l’ObjectDataSource à utiliser le `CategoriesBLL` classe ([cliquez pour afficher l’image en taille réelle](displaying-binary-data-in-the-data-web-controls-vb/_static/image6.png))


[![Récupérer la liste des catégories à l’aide de la méthode GetCategories()](displaying-binary-data-in-the-data-web-controls-vb/_static/image4.gif)](displaying-binary-data-in-the-data-web-controls-vb/_static/image7.png)

**Figure 4**: récupérer la liste de catégories à l’aide de la `GetCategories()` (méthode) ([cliquez pour afficher l’image en taille réelle](displaying-binary-data-in-the-data-web-controls-vb/_static/image8.png))


Après avoir terminé l’Assistant Configurer la Source de données, Visual Studio ajoute automatiquement un BoundField à la `Categories` GridView pour le `CategoryID`, `CategoryName`, `Description`, `NumberOfProducts`, et `BrochurePath` `DataColumn` s. Pour commencer, supprimez le `NumberOfProducts` BoundField depuis le `GetCategories()` requête de méthode s ne récupère pas de ces informations. Supprimer également le `CategoryID` BoundField et renommer le `CategoryName` et `BrochurePath` BoundFields `HeaderText` propriétés à la catégorie et Brochure, respectivement. Après avoir apporté ces modifications, votre balisage déclaratif s GridView et ObjectDataSource doit se présenter comme suit :


[!code-aspx[Main](displaying-binary-data-in-the-data-web-controls-vb/samples/sample1.aspx)]

Afficher cette page via un navigateur (voir Figure 5). Chacune des huit catégories est répertorié. Les sept catégories avec `BrochurePath` valeurs ont le `BrochurePath` valeur affichée dans le BoundField respectif. Mer, ce qui a un `NULL` valeur pour son `BrochurePath`, affiche une cellule vide.


[![Chaque catégorie s nom, Description et une valeur de BrochurePath est répertorié.](displaying-binary-data-in-the-data-web-controls-vb/_static/image5.gif)](displaying-binary-data-in-the-data-web-controls-vb/_static/image9.png)

**Figure 5**: catégorie s nom, Description, et `BrochurePath` valeur figure ([cliquez pour afficher l’image en taille réelle](displaying-binary-data-in-the-data-web-controls-vb/_static/image10.png))


Au lieu d’afficher le texte de la `BrochurePath` colonne, que vous souhaitez créer un lien vers la brochure. Pour ce faire, supprimez le `BrochurePath` BoundField et remplacez-le par un HyperLinkField. Définir la nouvelle s HyperLinkField `HeaderText` propriété Brochure, son `Text` propriété Brochure de la vue et son `DataNavigateUrlFields` propriété `BrochurePath`.


![Ajouter un HyperLinkField pour BrochurePath](displaying-binary-data-in-the-data-web-controls-vb/_static/image6.gif)

**Figure 6**: ajouter un HyperLinkField pour`BrochurePath`


Cette opération ajoute une colonne de liens au GridView, comme le montre la Figure 7. En cliquant sur un lien de vue Brochure soit afficher le fichier PDF directement dans le navigateur ou inviter l’utilisateur à télécharger le fichier, selon si un lecteur PDF est installé et les paramètres du navigateur s.


[![Une catégorie s Brochure peut être affiché en cliquant sur le lien d’affichage Brochure](displaying-binary-data-in-the-data-web-controls-vb/_static/image7.gif)](displaying-binary-data-in-the-data-web-controls-vb/_static/image11.png)

**Figure 7**: une catégorie s Brochure peuvent être affichées en cliquant sur le lien d’affichage Brochure ([cliquez pour afficher l’image en taille réelle](displaying-binary-data-in-the-data-web-controls-vb/_static/image12.png))


[![La catégorie s Brochure PDF s’affiche.](displaying-binary-data-in-the-data-web-controls-vb/_static/image8.gif)](displaying-binary-data-in-the-data-web-controls-vb/_static/image13.png)

**Figure 8**: s de la catégorie Brochure PDF est affichée ([cliquez pour afficher l’image en taille réelle](displaying-binary-data-in-the-data-web-controls-vb/_static/image14.png))


## <a name="hiding-the-view-brochure-text-for-categories-without-a-brochure"></a>Masquer le texte d’affichage Brochure pour les catégories sans une Brochure

Comme le montre la Figure 7, le `BrochurePath` HyperLinkField affiche son `Text` la valeur de propriété (vue Brochure) pour tous les enregistrements, indépendamment du fait que cet emplacement s non -`NULL` valeur pour `BrochurePath`. Bien entendu, si `BrochurePath` est `NULL`, le lien s’affiche sous forme de texte uniquement, comme c’est le cas avec la catégorie Seafood (voir la Figure 7). Au lieu d’afficher le texte Brochure de vue, il peut être utile d’avoir ces catégories sans un `BrochurePath` valeur afficher du texte de remplacement, comme Brochure aucune n’est disponible.

Pour fournir ce comportement, nous devons utiliser TemplateField dont le contenu est généré via un appel à une méthode de page qui émet la sortie appropriée en fonction de la `BrochurePath` valeur. Nous avons tout d’abord examiné cette technique de mise en forme dans le [à l’aide de TemplateField dans le contrôle GridView](../custom-formatting/using-templatefields-in-the-gridview-control-vb.md) didacticiel.

Activer la HyperLinkField en TemplateField en sélectionnant le `BrochurePath` HyperLinkField et puis en cliquant sur la convertir ce champ en TemplateField lien dans la boîte de dialogue Modifier les colonnes.


![Convertir le HyperLinkField en TemplateField](displaying-binary-data-in-the-data-web-controls-vb/_static/image9.gif)

**Figure 9**: convertir le HyperLinkField en TemplateField


Cette opération crée un TemplateField avec un `ItemTemplate` qui contient un site Web de lien hypertexte contrôle dont `NavigateUrl` propriété est liée à la `BrochurePath` valeur. Remplacez cette balise par un appel à la méthode `GenerateBrochureLink`, en passant la valeur de `BrochurePath`:


[!code-aspx[Main](displaying-binary-data-in-the-data-web-controls-vb/samples/sample2.aspx)]

Ensuite, créez un `Protected` méthode dans ASP.NET page classe code-behind de s nommé `GenerateBrochureLink` qui retourne un `String` et accepte un `Object` comme paramètre d’entrée.


[!code-vb[Main](displaying-binary-data-in-the-data-web-controls-vb/samples/sample3.vb)]

Cette méthode détermine si le passé dans `Object` valeur est une base de données `NULL` et, dans ce cas, retourne un message indiquant que la catégorie ne dispose pas d’une brochure. Sinon, s’il existe un `BrochurePath` valeur, il s’affiché dans un lien hypertexte. Notez que si le `BrochurePath` valeur est présente s passé dans le [ `ResolveUrl(url)` méthode](https://msdn.microsoft.com/library/system.web.ui.control.resolveurl.aspx). Cette méthode résout le passé dans *url*, en remplaçant le `~` caractère avec le chemin d’accès virtuel approprié. Par exemple, si l’application est rattachée à `/Tutorial55`, `ResolveUrl("~/Brochures/Meats.pdf")` retournera `/Tutorial55/Brochures/Meat.pdf`.

La figure 10 illustre la page après que ces modifications ont été appliquées. Notez que la catégorie Seafood s `BrochurePath` champ affiche maintenant le texte Brochure aucune n’est disponible.


[![Texte non Brochure disponibles s’affiche pour les catégories sans un](displaying-binary-data-in-the-data-web-controls-vb/_static/image10.gif)](displaying-binary-data-in-the-data-web-controls-vb/_static/image15.png)

**La figure 10**: le texte non Brochure disponibles s’affiche pour les catégories sans un ([cliquez pour afficher l’image en taille réelle](displaying-binary-data-in-the-data-web-controls-vb/_static/image16.png))


## <a name="step-3-adding-a-web-page-to-display-a-category-s-picture"></a>Étape 3 : Ajout d’une Page Web pour afficher une image de catégorie s

Lorsqu’un utilisateur visite une page ASP.NET, ils reçoivent les pages HTML ASP.NET. Le code HTML reçu est uniquement du texte et ne contient-elle pas toutes les données binaires. Toutes les données binaires supplémentaires, telles que les images, les fichiers audio, Macromedia Flash, incorporé vidéos du lecteur Windows Media et ainsi de suite, existent en tant que ressources distincts sur le serveur web. Le code HTML contient des références à ces fichiers, mais n’inclut pas le contenu réel des fichiers.

Par exemple, au format HTML la `<img>` élément est utilisé pour faire référence à une image, avec la `src` attribut pointant vers le fichier image comme suit :


[!code-html[Main](displaying-binary-data-in-the-data-web-controls-vb/samples/sample4.html)]

Lorsqu’un navigateur reçoit ce code HTML, il effectue une autre requête sur le serveur web pour récupérer le contenu binaire du fichier image, qu’il affiche ensuite dans le navigateur. Le même concept s’applique à toutes les données binaires. À l’étape 2, la brochure n'a pas été envoyée vers le bas dans le navigateur en tant que partie du balisage HTML de s de page. Au lieu de cela, le rendu HTML fournie des liens hypertexte qui, lorsque vous cliquez dessus, a provoqué le navigateur demander le document PDF directement.

Pour afficher ou autoriser les utilisateurs à télécharger des données binaires qui se trouvent dans la base de données, nous devons créer une page web qui retourne les données. Pour notre application, qu’un seul champ de données binaires là s stockée directement dans l’image de base de données s de la catégorie. Par conséquent, nous avons besoin d’une page qui, lorsqu’elle est appelée, retourne les données d’image pour une catégorie particulière.

Ajouter une nouvelle page ASP.NET pour le `BinaryData` dossier nommé `DisplayCategoryPicture.aspx`. Dans ce cas, laissez la case à cocher de la page maître sélectionnez désactivée. Cette page attend un `CategoryID` valeur dans la chaîne de requête et retourne les données binaires de cette catégorie s `Picture` colonne. Étant donné que cette page retourne des données binaires et rien d’autre, il ne doit pas tout balisage dans la section HTML. Par conséquent, cliquez sur l’onglet Source dans l’angle inférieur gauche et supprimer toutes les marques de la page s à l’exception de la `<%@ Page %>` la directive. Autrement dit, `DisplayCategoryPicture.aspx` balisage déclaratif de s doit être composé d’une seule ligne :


[!code-aspx[Main](displaying-binary-data-in-the-data-web-controls-vb/samples/sample5.aspx)]

Si vous voyez le `MasterPageFile` l’attribut dans la `<%@ Page %>` directive, supprimez-le.

Dans la classe code-behind de la page s, ajoutez le code suivant à la `Page_Load` Gestionnaire d’événements :


[!code-vb[Main](displaying-binary-data-in-the-data-web-controls-vb/samples/sample6.vb)]

Ce code commence par la lecture dans le `CategoryID` valeur de chaîne de requête dans une variable nommée `categoryID`. Ensuite, les données d’image sont récupérées via un appel à la `CategoriesBLL` classe s `GetCategoryWithBinaryDataByCategoryID(categoryID)` (méthode). Ces données sont retournées au client à l’aide de la `Response.BinaryWrite(data)` (méthode), mais avant que cela est appelée, le `Picture` en-tête de colonne valeur s OLE doit être supprimé. Cela est accompli en créant un `Byte` tableau nommé `strippedImageData` de contenir précisément 78 caractères inférieur à ce qui est dans le `Picture` colonne. Le [ `Array.Copy` méthode](https://msdn.microsoft.com/library/z50k9bft.aspx) est utilisé pour copier les données à partir de `category.Picture` recommencer à la position 78 à `strippedImageData`.

Le `Response.ContentType` propriété spécifie le [type MIME](http://en.wikipedia.org/wiki/MIME) du contenu qui est retourné afin que le navigateur sait comment rendre. Étant donné que la `Categories` table s `Picture` la colonne est une image bitmap, la type MIME de la bitmap est utilisée ici (image/bmp). Si vous omettez le type MIME, la plupart des navigateurs s’affichera toujours l’image correctement, car il peuvent déduire le type en fonction du contenu des données binaires de s de fichier image. Toutefois, il s prudente à inclure MIME de type lorsque cela est possible. Consultez le [site Web de s Internet Assigned Numbers Authority](http://www.iana.org/) pour une liste complète des [types de média MIME](http://www.iana.org/assignments/media-types/).

Avec cette page est créée, une image de catégorie particulier s peut être affichée en vous rendant sur `DisplayCategoryPicture.aspx?CategoryID=categoryID`. La figure 11 illustre l’image de catégorie s boissons, qui peut être consulté dans `DisplayCategoryPicture.aspx?CategoryID=1`.


[![Les s catégorie boissons Qu'image s’affiche.](displaying-binary-data-in-the-data-web-controls-vb/_static/image11.gif)](displaying-binary-data-in-the-data-web-controls-vb/_static/image17.png)

**Figure 11**: s de la catégorie boissons image est affichée ([cliquez pour afficher l’image en taille réelle](displaying-binary-data-in-the-data-web-controls-vb/_static/image18.png))


Si, lors de la visite `DisplayCategoryPicture.aspx?CategoryID=categoryID`, vous obtenez une exception qui lit impossible à l’objet de conversion de type « System.DBNull » en type « System.Byte [] », il existe deux éléments qui peuvent être la cause. Tout d’abord, le `Categories` table s `Picture` colonne n’autorise pas `NULL` valeurs. Le `DisplayCategoryPicture.aspx` page, suppose toutefois, il est non -`NULL` valeur présente. Le `Picture` propriété de la `CategoriesDataTable` ne sont pas accessibles directement si elle a un `NULL` valeur. Si vous ne souhaitez pas autoriser `NULL` les valeurs pour la `Picture` colonne, d souhaitez-vous inclure la condition suivante :


[!code-vb[Main](displaying-binary-data-in-the-data-web-controls-vb/samples/sample7.vb)]

Le code ci-dessus suppose que ceci s certaines images fichier nommé `NoPictureAvailable.gif` dans le `Images` dossier que vous souhaitez afficher pour ces catégories sans une image.

Cette exception peut également se produire si le `CategoriesTableAdapter` s `GetCategoryWithBinaryDataByCategoryID` méthode s `SELECT` instruction a été rétabli à la liste de colonnes de la requête principale s, ce qui peut se produire si vous utilisez des instructions SQL ad hoc et que vous avez déjà exécuter à nouveau l’Assistant TableAdapter s requête principale. Assurez-vous que `GetCategoryWithBinaryDataByCategoryID` méthode s `SELECT` instruction inclut toujours le `Picture` colonne.

> [!NOTE]
> Chaque fois que le `DisplayCategoryPicture.aspx` est visitée, la base de données est accessible et que les données d’image de la catégorie spécifiée s sont retournées. Si le t catégorie s image n’a changé depuis la dernière visite, il ne s’agit efforts inutiles. Heureusement, permet le protocole HTTP pour *conditionnelle Obtient*. Avec une opération GET conditionnelle, le client qui effectue la requête HTTP envoie le long d’un [ `If-Modified-Since` en-tête HTTP](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) qui fournit la date et l’heure, le client dernière récupération de cette ressource à partir du serveur web. Si le contenu n’a pas changé depuis cette spécifié de date, le serveur web peut répondre avec une [code d’état non modifié (304)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) et renoncer à renvoyer le contenu de la ressource demandée s. En bref, cette technique évite d’avoir à envoyer de contenu pour une ressource si elle n’a pas été modifié depuis le dernier accès au client qu’il le serveur web.


Pour implémenter ce comportement, toutefois, vous devez ajouter les un `PictureLastModified` colonne à la `Categories` table pour la capture lorsque la `Picture` colonne dernière mise à jour, ainsi que le code de vérification de la `If-Modified-Since` en-tête. Pour plus d’informations sur la `If-Modified-Since` en-tête et le workflow GET conditionnel, consultez [HTTP GET conditionnel pour les pirates informatiques RSS](http://fishbowl.pastiche.org/2002/10/21/http_conditional_get_for_rss_hackers) et [A plus approfondis examiner effectuant des requêtes HTTP dans une Page ASP.NET](http://aspnet.4guysfromrolla.com/articles/122204-1.aspx).

## <a name="step-4-displaying-the-category-pictures-in-a-gridview"></a>Étape 4 : Afficher les images de catégorie dans un GridView

Maintenant que nous avons une page web pour afficher une image de catégorie particulier s, nous pouvons afficher à l’aide de la [contrôle Image Web](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/ctrlref/standard/image.aspx) ou HTML `<img>` élément pointant vers `DisplayCategoryPicture.aspx?CategoryID=categoryID`. Les images dont l’URL est déterminée par les données de base de données peuvent être affichées dans le GridView ou DetailsView à l’aide de la ImageField. Le ImageField contient `DataImageUrlField` et `DataImageUrlFormatString` propriétés qui fonctionnent comme les s HyperLinkField `DataNavigateUrlFields` et `DataNavigateUrlFormatString` propriétés.

S permettent d’augmenter la `Categories` GridView dans `DisplayOrDownloadData.aspx` en ajoutant un ImageField pour afficher chaque image de catégorie s. Simplement ajouter la ImageField et définir son `DataImageUrlField` et `DataImageUrlFormatString` propriétés `CategoryID` et `DisplayCategoryPicture.aspx?CategoryID={0}`, respectivement. Cela crée une colonne GridView qui restitue une `<img>` élément dont `src` les références d’attribut `DisplayCategoryPicture.aspx?CategoryID={0}`, où {0} est remplacé par la ligne GridView s `CategoryID` valeur.


![Ajouter un ImageField au contrôle GridView](displaying-binary-data-in-the-data-web-controls-vb/_static/image12.gif)

**Figure 12**: ajouter un ImageField au contrôle GridView


Après avoir ajouté le ImageField, votre syntaxe déclarative de GridView s doit être semblable à soothe suivant :


[!code-aspx[Main](displaying-binary-data-in-the-data-web-controls-vb/samples/sample8.aspx)]

Prenez un moment pour afficher cette page via un navigateur. Notez la façon dont chaque enregistrement inclut désormais une image pour la catégorie.


[![La catégorie s image est affichée pour chaque ligne](displaying-binary-data-in-the-data-web-controls-vb/_static/image13.gif)](displaying-binary-data-in-the-data-web-controls-vb/_static/image19.png)

**Figure 13**: s de catégorie de l’image est affichée pour chaque ligne ([cliquez pour afficher l’image en taille réelle](displaying-binary-data-in-the-data-web-controls-vb/_static/image20.png))


## <a name="summary"></a>Récapitulatif

Dans ce didacticiel, nous avons examiné comment présenter des données binaires. Comment les données sont présentées varie selon le type de données. Pour les fichiers de brochure PDF, nous avons proposé à l’utilisateur une Brochure vue lien qui, lorsque vous cliquez dessus, a mis l’utilisateur directement dans le fichier PDF. Pour l’image de s catégorie, nous avons tout d’abord créer une page pour récupérer et retourner les données binaires à partir de la base de données et ensuite utilisé cette page pour afficher chaque image s de catégorie dans un GridView.

Maintenant que nous ve examiné comment afficher les données binaires, nous re prêt à examiner comment effectuer une insertion, de mises à jour et suppressions sur la base de données avec les données binaires. Dans l’étape suivante du didacticiel, nous allons examiner comment associer un fichier téléchargé à l’enregistrement correspondant de la base de données. Dans le didacticiel après cela, nous allons voir comment mettre à jour les données binaires existantes, ainsi que comment supprimer les données binaires lors de son enregistrement associé est supprimé.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Teresa Murphy et Dave Gardner. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](uploading-files-vb.md)
[Suivant](including-a-file-upload-option-when-adding-a-new-record-vb.md)
