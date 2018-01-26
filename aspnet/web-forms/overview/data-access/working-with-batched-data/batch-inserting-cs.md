---
uid: web-forms/overview/data-access/working-with-batched-data/batch-inserting-cs
title: "Lot d’insertion (c#) | Documents Microsoft"
author: rick-anderson
description: "Découvrez comment insérer plusieurs enregistrements de base de données en une seule opération. Dans la couche d’Interface utilisateur, nous étendre le contrôle GridView pour autoriser l’utilisateur à entrer plusieurs de n..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/26/2007
ms.topic: article
ms.assetid: cf025e08-48fc-4385-b176-8610aa7b5565
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/working-with-batched-data/batch-inserting-cs
msc.type: authoredcontent
ms.openlocfilehash: 9dc18e259da24d71464a156a70a85cfc9a1745ce
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="batch-inserting-c"></a>Lot d’insertion (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_66_CS.zip) ou [télécharger le PDF](batch-inserting-cs/_static/datatutorial66cs1.pdf)

> Découvrez comment insérer plusieurs enregistrements de base de données en une seule opération. Dans la couche d’Interface utilisateur, nous étendre le contrôle GridView pour autoriser l’utilisateur à entrer plusieurs nouveaux enregistrements. Dans la couche d’accès aux données, nous encapsuler les plusieurs opérations d’insertion dans une transaction pour vous assurer que toutes les insertions réussissent ou que toutes les insertions sont restaurées.


## <a name="introduction"></a>Introduction

Dans le [mise à jour par lot](batch-updating-cs.md) didacticiel, nous avons étudié personnalisation du contrôle GridView pour présenter une interface où plusieurs enregistrements ont été modifiable. L’utilisateur visite de la page peut effectuer une série de modifications et puis, en un seul clic, effectuer une mise à jour par lots. Dans les situations où les utilisateurs jour généralement plusieurs enregistrements d’un coup, une telle interface peut enregistrer des clics innombrables et les changements de contexte de clavier de la souris par rapport à la valeur par défaut par ligne des fonctionnalités de modification qui ont été étudiées tout d’abord dans le [un Vue d’ensemble de l’insertion, mise à jour et suppression des données](../editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-cs.md) didacticiel.

Ce concept peut également être appliqué lors de l’ajout d’enregistrements. Imaginez qu’ici chez Northwind Traders nous reçoivent généralement expéditions de fournisseurs qui contiennent un nombre de produits pour une catégorie particulière. Par exemple, nous pouvons réception de six thé différentes et des produits de café de Tokyo Traders. Si un utilisateur entre les six produits un à la fois via un contrôle DetailsView, ils doivent choisir la plupart des mêmes valeurs indéfiniment : ils doivent choisir la même catégorie (boissons), le même fournisseur (Tokyo Traders), le même arrêté (de valeur False) et les mêmes unités sur la valeur d’ordre (0). Cette entrée de données répétitives n’est pas uniquement beaucoup de temps, mais est sujette aux erreurs.

Avec un peu de travail, nous pouvons créer un lot d’insertion d’interface qui permet à l’utilisateur de choisir le fournisseur et la catégorie une seule fois, entrez une série de noms de produits et des prix unitaires, puis cliquez sur un bouton pour ajouter les nouveaux produits à la base de données (voir Figure 1). Chaque produit est ajoutée, ses `ProductName` et `UnitPrice` des champs de données sont affectés les valeurs entrées dans les zones de texte, tandis que son `CategoryID` et `SupplierID` valeurs, les valeurs sont affectées à partir des compréhension des listes sur le haut de la forme. Le `Discontinued` et `UnitsOnOrder` valeurs sont définies sur les valeurs codées en dur des `false` et 0, respectivement.


[![L’Interface d’insertion de lot](batch-inserting-cs/_static/image2.png)](batch-inserting-cs/_static/image1.png)

**Figure 1**: l’Interface d’insertion de lot ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image3.png))


Dans ce didacticiel, nous allons créer une page qui implémente le lot d’insertion d’interface, comme indiqué dans la Figure 1. Comme avec les deux didacticiels précédents, nous encapsule les insertions dans la portée d’une transaction pour garantir l’atomicité. Let s commencer !

## <a name="step-1-creating-the-display-interface"></a>Étape 1 : Création de l’Interface d’affichage

Ce didacticiel se compose d’une seule page est divisée en deux zones : une zone d’affichage et une région d’insertion. L’interface d’affichage, nous allons créer dans cette étape, affiche les produits dans un GridView et inclut un bouton intitulé du processus de livraison du produit. Lorsque ce bouton est activé, l’interface d’affichage est remplacée par l’interface d’insertion, qui est affichée dans la Figure 1. L’interface d’affichage retourne après l’ajout de produits à partir de l’expédition ou cliqué sur les boutons Annuler. Nous allons créer l’interface d’insertion à l’étape 2.

Lors de la création d’une page qui possède deux interfaces, seule l’une qui est visible à la fois, chaque interface est généralement placé dans un [contrôle du Panneau de configuration Web](http://www.w3schools.com/aspnet/control_panel.asp), qui sert de conteneur pour d’autres contrôles. Par conséquent, notre page aura deux contrôles de panneau un pour chaque interface.

Commencez par ouvrir le `BatchInsert.aspx` page dans le `BatchData` faites glisser un panneau de configuration à partir de la boîte à outils vers le concepteur (voir Figure 2). Définir le panneau de configuration s `ID` propriété `DisplayInterface`. Lors de l’ajout du Panneau de configuration vers le concepteur, son `Height` et `Width` sont affectées aux propriétés 50px et 125px, respectivement. Vider ces valeurs de propriété à partir de la fenêtre Propriétés.


[![Faites glisser un panneau de configuration à partir de la boîte à outils vers le Concepteur](batch-inserting-cs/_static/image5.png)](batch-inserting-cs/_static/image4.png)

**Figure 2**: faites glisser un panneau de configuration à partir de la boîte à outils vers le concepteur ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image6.png))


Ensuite, faites glisser un contrôle bouton et GridView dans le panneau de configuration. Définir le bouton s `ID` propriété `ProcessShipment` et son `Text` propriété livraison du produit. Définir le contrôle GridView s `ID` propriété `ProductsGrid` et, à partir de sa balise active, la lier à un nouveau ObjectDataSource nommé `ProductsDataSource`. Configurer l’ObjectDataSource à extraire ses données à partir de la `ProductsBLL` classe s `GetProducts` (méthode). Étant donné que cette GridView est utilisé uniquement pour afficher des données, définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None). Cliquez sur Terminer pour terminer l’Assistant Configurer la Source de données.


[![Afficher les données retournées à partir de la méthode GetProducts ProductsBLL classe s](batch-inserting-cs/_static/image8.png)](batch-inserting-cs/_static/image7.png)

**Figure 3**: afficher les données retournées à partir de la `ProductsBLL` classe s `GetProducts` (méthode) ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image9.png))


[![Définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None)](batch-inserting-cs/_static/image11.png)](batch-inserting-cs/_static/image10.png)

**Figure 4**: définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None) ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image12.png))


Après avoir terminé l’Assistant ObjectDataSource, Visual Studio ajoute BoundFields et un CheckBoxField pour les champs de données de produit. Supprimer tout sauf la `ProductName`, `CategoryName`, `SupplierName`, `UnitPrice`, et `Discontinued` champs. N’hésitez pas à effectuer toutes les personnalisations esthétiques. J’ai décidé de mettre en forme le `UnitPrice` champ en tant que valeur monétaire, de réorganiser les champs et plusieurs des champs renommés `HeaderText` valeurs. Également configurer le contrôle GridView pour inclure la pagination et le tri de prise en charge en activant les cases à cocher Activer la pagination et activer le tri dans la balise active de GridView s.

Après l’ajout de contrôles du Panneau de configuration, bouton, GridView et ObjectDataSource et personnaliser les champs de s GridView, votre balisage déclaratif s de page doit ressembler à ce qui suit :


[!code-aspx[Main](batch-inserting-cs/samples/sample1.aspx)]

Notez que le balisage pour le bouton et un GridView apparaissent au sein de l’ouverture et de fermeture `<asp:Panel>` balises. Étant donné que ces contrôles sont dans le `DisplayInterface` Panneau de configuration, nous pouvons les masquer en définissant simplement le panneau s `Visible` propriété `false`. Étape 3 examine la modification par programme le panneau s `Visible` propriété en réponse à un bouton Cliquez ici pour afficher une interface tout en masquant l’autre.

Prenez un moment pour afficher la progression via un navigateur. Comme le montre la Figure 5, vous devez voir un bouton de la livraison du produit au-dessus d’un GridView qui répertorie les dix produits à la fois.


[![Le contrôle GridView répertorie les produits et offre le tri et la pagination des fonctionnalités](batch-inserting-cs/_static/image14.png)](batch-inserting-cs/_static/image13.png)

**Figure 5**: le contrôle GridView répertorie les produits et offre le tri et les fonctionnalités de pagination ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image15.png))


## <a name="step-2-creating-the-inserting-interface"></a>Étape 2 : Création de l’Interface de l’insertion

Avec l’interface d’affichage complet, nous re prêt à créer l’interface d’insertion. Pour ce didacticiel, permettent de créer une interface d’insertion qui vous demande d’une seule valeur de fournisseur et la catégorie, puis permet à l’utilisateur à entrer jusqu'à cinq noms de produits et les valeurs des prix unitaires de s. Avec cette interface, l’utilisateur peut ajouter une à cinq nouveaux produits partagent tous la même catégorie et le fournisseur, mais dont les prix et les noms de produit unique.

Démarrage en faisant glisser un panneau de configuration à partir de la boîte à outils vers le concepteur, placer sous existants `DisplayInterface` Panneau de configuration. Définir le `ID` cette propriété nouvellement ajoutés Panneau de configuration pour `InsertingInterface` et définir son `Visible` propriété `false`. Nous allons ajouter le code qui définit les `InsertingInterface` panneau s `Visible` propriété `true` à l’étape 3. Désactivez également le panneau s `Height` et `Width` les valeurs de propriété.

Ensuite, nous avons besoin créer l’interface d’insertion qui a été indiqué dans la Figure 1. Cette interface peut être créée par le biais de diverses techniques HTML, mais nous allons utiliser un index relativement simple : une table en quatre colonnes et d’une ligne sept.

> [!NOTE]
> Lors de la saisie de balisage HTML `<table>` éléments, je préfère utiliser la vue de Source. Lors de Visual Studio dispose d’outils permettant d’ajouter `<table>` éléments par le biais du concepteur, le concepteur semble tout trop souhaite injecter qui pour `style` paramètres dans le balisage. Une fois que j’ai créé la `<table>` balisage, généralement renvoyer au concepteur pour ajouter des contrôles Web et de définir leurs propriétés. Lors de la création de tables avec des colonnes et lignes, je préfère à l’aide de code HTML statique plutôt que la [contrôle Web Table](https://msdn.microsoft.com/library/system.web.ui.webcontrols.table.aspx) , car tous les contrôles Web placés dans un contrôle Web de la Table est accessible uniquement à l’aide de la `FindControl("controlID")` modèle. Contrôles Web de la Table faire, toutefois, utiliser des tables dimensionnées de manière dynamique (ceux dont lignes ou les colonnes est basés sur certains de base de données ou les critères définis par l’utilisateur), depuis le Web de la Table contrôle peut être construit par programme.


Entrez le balisage suivant dans le `<asp:Panel>` balises de la `InsertingInterface` Panneau de configuration :


[!code-html[Main](batch-inserting-cs/samples/sample2.html)]

Cela `<table>` balisage n’inclut pas tous les contrôles Web encore, nous allons ajouter ceux bientôt. Notez que chaque `<tr>` élément contient un paramètre de classe CSS particulier : `BatchInsertHeaderRow` pour la ligne d’en-tête dans lequel le fournisseur et la compréhension des listes de catégorie passera ; `BatchInsertFooterRow` pour la ligne de pied de page dans laquelle ajouter des produits à partir d’expédition et annuler les boutons passera ; et remplacement `BatchInsertRow` et `BatchInsertAlternatingRow` valeurs pour les lignes contenant le produit et l’unité de prix des contrôles de zone de texte. Vous venez de créer des classes CSS correspondantes dans le `Styles.css` fichier pour permettre à l’interface insertion une apparence semblable à GridView et DetailsView nous contrôle ve utilisée tout au long de ces didacticiels. Ces classes CSS sont indiquées ci-dessous.


[!code-css[Main](batch-inserting-cs/samples/sample3.css)]

Avec ce balisage est entré, revenir à la vue de conception. Cela `<table>` doit être une table en quatre colonnes et d’une ligne sept dans le concepteur, comme le montre la Figure 6.


[![L’Interface d’insertion est composé d’une colonne de quatre, Table ligne sept](batch-inserting-cs/_static/image17.png)](batch-inserting-cs/_static/image16.png)

**Figure 6**: l’Interface d’insertion est composé d’une colonne de quatre, Table ligne sept ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image18.png))


Nous re maintenant prêt à ajouter des contrôles Web à l’interface d’insertion. Faites glisser deux compréhension des listes à partir de la boîte à outils dans les cellules appropriées dans la table une pour le fournisseur et l’autre pour la catégorie.

Définir le fournisseur DropDownList s `ID` propriété `Suppliers` et la lier à un nouveau ObjectDataSource nommé `SuppliersDataSource`. Configurer le nouveau ObjectDataSource pour récupérer ses données à partir de la `SuppliersBLL` classe s `GetSuppliers` (méthode) et l’ensemble de la mise à jour onglet liste de déroulante s (aucun). Cliquez sur Terminer pour terminer l’Assistant.


[![Configurer pour utiliser la méthode GetSuppliers de la classe SuppliersBLL s ObjectDataSource](batch-inserting-cs/_static/image20.png)](batch-inserting-cs/_static/image19.png)

**Figure 7**: configurer l’ObjectDataSource à utiliser le `SuppliersBLL` classe s `GetSuppliers` (méthode) ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image21.png))


Ont le `Suppliers` DropDownList affichage le `CompanyName` champ de données et utilisez le `SupplierID` de champ de données en tant que son `ListItem` valeurs s.


[![Afficher le champ de données de la société et utiliser SupplierID comme valeur](batch-inserting-cs/_static/image23.png)](batch-inserting-cs/_static/image22.png)

**Figure 8**: afficher le `CompanyName` champ de données et utilisez `SupplierID` comme valeur ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image24.png))


Nom de la deuxième DropDownList `Categories` et la lier à un nouveau ObjectDataSource nommé `CategoriesDataSource`. Configurer le `CategoriesDataSource` ObjectDataSource à utiliser le `CategoriesBLL` classe s `GetCategories` méthode ; l’ensemble de la liste déroulante répertorie dans la mise à jour et supprimer des onglets (aucun) et cliquez sur Terminer pour terminer l’Assistant. Enfin, disposer de l’affichage DropDownList le `CategoryName` champ de données et utilisez le `CategoryID` comme valeur.

Une fois que ces deux compréhension des listes ont été ajoutés et liés à ObjectDataSources correctement configuré, votre écran doit ressembler à la Figure 9.


[![La ligne d’en-tête contient désormais les fournisseurs et les catégories compréhension des listes](batch-inserting-cs/_static/image26.png)](batch-inserting-cs/_static/image25.png)

**Figure 9**: l’en-tête de ligne contient maintenant le `Suppliers` et `Categories` compréhension des listes ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image27.png))


Nous devons maintenant créer les zones de texte pour collecter le nom et le prix de chaque produit. Faites glisser un contrôle de zone de texte à partir de la boîte à outils vers le concepteur pour les lignes de nom et le prix du cinq produit. Définir le `ID` propriétés des zones de texte à `ProductName1`, `UnitPrice1`, `ProductName2`, `UnitPrice2`, `ProductName3`, `UnitPrice3`, et ainsi de suite.

Ajouter un CompareValidator après chacune des zones de texte, définition du prix unitaire le `ControlToValidate` propriété approprié `ID`. Définir également la `Operator` propriété `GreaterThanEqual`, `ValueToCompare` à 0, et `Type` à `Currency`. Ces paramètres, demandez à CompareValidator pour vous assurer que le prix, si vous avez entré, est une valeur de devise valide qui est supérieur ou égal à zéro. Définir le `Text` propriété \*, et `ErrorMessage` le prix doit être supérieur ou égal à zéro. En outre, veuillez omettre de tous les symboles monétaires.

> [!NOTE]
> L’interface d’insertion n’inclut pas tous les contrôles RequiredFieldValidator, même si le `ProductName` champ dans le `Products` table de base de données n’autorise pas `NULL` valeurs. Il s’agit, car nous souhaitons Entrez jusqu'à cinq produits à l’utilisateur. Par exemple, si l’utilisateur à fournir le produit nom et le prix unitaire pour les trois premières lignes, en laissant les deux dernières lignes est vide, nous d simplement ajouter trois nouveaux produits au système. Étant donné que `ProductName` est nécessaire, toutefois, nous devons vérifier par programmation pour garantir que si un prix unitaire est entrée qu’une valeur de nom de produit correspondant est fournie. Nous allons aborder cette vérification à l’étape 4.


Lors de la validation de l’entrée d’utilisateur s, CompareValidator signale des données non valides si la valeur contient un symbole de devise. Ajoutez un $ devant chacune des zones de texte comme un indice visuel qui indique à l’utilisateur d’omettre le symbole monétaire lorsque vous entrez le prix du prix unitaire.

Enfin, ajoutez un contrôle ValidationSummary dans le `InsertingInterface` Panneau de configuration, les paramètres de son `ShowMessageBox` propriété `true` et son `ShowSummary` propriété `false`. Avec ces paramètres, si l’utilisateur entre une valeur du prix unitaire non valide, un astérisque apparaît en regard les contrôles de zone de texte qui posent problème et le contrôle ValidationSummary affiche un messagebox côté client qui affiche le message d’erreur que nous avons spécifié précédemment.

À ce stade, votre écran doit ressembler à la Figure 10.


[![L’Interface insertion inclut désormais les zones de texte pour les produits nom et le prix](batch-inserting-cs/_static/image29.png)](batch-inserting-cs/_static/image28.png)

**La figure 10**: l’insertion d’Interface maintenant inclut des zones de texte pour les noms de produits et les prix ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image30.png))


Ensuite, nous devons ajouter les produits à ajouter à partir de l’expédition et annuler des boutons à la ligne de pied de page. Faites glisser deux contrôles bouton à partir de la boîte à outils dans le pied de page de l’interface d’insertion, définissant les boutons `ID` propriétés `AddProducts` et `CancelButton` et `Text` propriétés pour ajouter des produits à partir d’expédition et Annuler, respectivement. En outre, définissez la `CancelButton` contrôle s `CausesValidation` propriété `false`.

Enfin, nous devons ajouter un contrôle Web Label qui affichera les messages d’état pour les deux interfaces. Par exemple, lorsqu’un utilisateur ajoute correctement une nouvelle livraison de produits, vous souhaitez revenir à l’interface d’affichage et afficher un message de confirmation. Si, toutefois, l’utilisateur fournit un prix pour un nouveau produit, mais laisse le nom de produit, nous devons afficher un message d’avertissement depuis le `ProductName` champ est obligatoire. Étant donné que nous avons besoin de ce message à afficher pour les deux interfaces, placez-la en haut de la page en dehors de panneaux.

Faites glisser un contrôle Web Label à partir de la boîte à outils vers le haut de la page dans le concepteur. Définir le `ID` propriété `StatusLabel`, désactivez les le `Text` propriété et définissez la `Visible` et `EnableViewState` propriétés à `false`. Comme nous l’avons vu dans les didacticiels précédents, définissant la `EnableViewState` propriété `false` permet par programmation modifier les valeurs de propriété Label s et demandez-lui de revenir automatiquement à leurs valeurs par défaut lors de la publication ultérieure. Cela simplifie le code pour afficher un message d’état en réponse à des actions de l’utilisateur qui disparaît lors de la publication ultérieure. Enfin, définissez la `StatusLabel` contrôle s `CssClass` propriété à avertissement, qui est le nom d’une classe CSS définie dans `Styles.css` qui affiche le texte dans une police de grande taille, italique, gras, rouge.

La figure 11 illustre le concepteur Visual Studio après que l’étiquette a été ajoutée et configurée.


[![Placez le contrôle StatusLabel au-dessus des deux contrôles de panneau de configuration](batch-inserting-cs/_static/image32.png)](batch-inserting-cs/_static/image31.png)

**Figure 11**: Place le `StatusLabel` contrôle ci-dessus les deux contrôles de panneau ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image33.png))


## <a name="step-3-switching-between-the-display-and-inserting-interfaces"></a>Étape 3 : Basculer entre l’affichage et l’insertion d’Interfaces

À ce stade, nous avons terminé le balisage pour notre affichage et en insérant des interfaces, mais nous re reste toujours deux tâches :

- Basculer entre l’affichage et l’insertion des interfaces
- Ajout de produits dans l’expédition de la base de données

Actuellement, l’interface d’affichage est visible, mais l’interface d’insertion est masquée. C’est parce que le `DisplayInterface` panneau s `Visible` est définie sur `true` (la valeur par défaut), alors que le `InsertingInterface` panneau s `Visible` est définie sur `false`. Pour basculer entre les deux interfaces, il suffit d’activer/désactiver chaque contrôle s `Visible` valeur de propriété.

Vous souhaitez déplacer à partir de l’interface d’affichage à l’interface d’insertion lorsque l’utilisateur clique sur le bouton de la livraison du produit. Par conséquent, créer un gestionnaire d’événements pour ce bouton s `Click` événement qui contient le code suivant :


[!code-csharp[Main](batch-inserting-cs/samples/sample4.cs)]

Ce code masque simplement la `DisplayInterface` Panneau de configuration et affiche le `InsertingInterface` Panneau de configuration.

Ensuite, créez des gestionnaires d’événements pour l’ajout de produits à partir de contrôles d’expédition et le bouton Annuler dans l’interface d’insertion. Quand un de ces boutons est activé, nous devons revenir à l’interface d’affichage. Créer `Click` gestionnaires d’événements pour les deux contrôles boutons afin qu’ils appellent `ReturnToDisplayInterface`, une méthode, nous allons ajouter quelques instants. En plus de masquage le `InsertingInterface` Panneau de configuration et l’affichage le `DisplayInterface` Panneau de configuration, le `ReturnToDisplayInterface` méthode doit retourner les contrôles Web à leur état avant modification. Cela implique la définition des compréhension des listes `SelectedIndex` propriétés à 0 et supprimant le `Text` propriétés des contrôles de zone de texte.

> [!NOTE]
> Considérez ce qui peut se produire si nous ne t revenir aux contrôles à leur état de modification préalable avant de retourner à l’interface d’affichage. Un utilisateur peut cliquer sur le bouton de la livraison du produit, entrez les produits à partir de l’expédition, puis cliquez sur Ajouter des produits à partir de l’expédition. Cela serait ajouter les produits et renvoyer l’utilisateur à l’interface d’affichage. À ce stade, l’utilisateur souhaite ajouter une autre expédition. Lorsque vous cliquez sur le bouton de la livraison du produit processus qu'elles restaurent à l’interface insertion mais DropDownList sélections et des valeurs de la zone de texte sont encore remplis avec leurs valeurs précédentes.


[!code-csharp[Main](batch-inserting-cs/samples/sample5.cs)]

Les deux `Click` gestionnaires d’événements simplement appellent le `ReturnToDisplayInterface` (méthode), bien que nous allons retourner à l’ajout de produits à partir de l’expédition `Click` Gestionnaire d’événements dans l’étape 4 et ajoutez le code pour enregistrer les produits. `ReturnToDisplayInterface`démarre en retournant le `Suppliers` et `Categories` compréhension des listes à leurs première options. Les deux constantes `firstControlID` et `lastControlID` marquer le début et fin des valeurs d’index de contrôle utilisés pour nommer le produit nom et le prix unitaire zones de texte de l’insertion de l’interface et sont utilisés dans les limites de la `for` boucle qui définit les `Text`sauvegarder des propriétés des contrôles de zone de texte à une chaîne vide. Enfin, les panneaux `Visible` des propriétés sont rétablies afin que l’interface d’insertion est masquée et l’interface d’affichage indiqué.

Prenez un moment pour tester cette page dans un navigateur. Lors de la visite de tout d’abord la page, vous devez voir l’interface d’affichage comme illustré dans la Figure 5. Cliquez sur le bouton de la livraison du produit. La page est publiée et que vous devez maintenant voir l’interface insertion comme indiqué dans la Figure 12. En cliquant sur les deux produits ajouter à partir des boutons d’expédition ou sur Annuler pour revenir à l’interface d’affichage.

> [!NOTE]
> Lorsque vous affichez l’interface d’insertion, prenez un moment pour tester le CompareValidators sur le prix unitaire zones de texte. Vous devez voir un messagebox côté client d’avertissement lorsque vous cliquez sur Ajouter des produits à partir de bouton d’expédition avec les valeurs de devise non valide ou le prix d’une valeur inférieure à zéro.


[![L’Interface d’insertion s’affiche après avoir cliqué sur le bouton de livraison de produit de processus](batch-inserting-cs/_static/image35.png)](batch-inserting-cs/_static/image34.png)

**Figure 12**: l’Interface d’insertion s’affiche après avoir cliqué sur le bouton de livraison de produit de processus ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image36.png))


## <a name="step-4-adding-the-products"></a>Étape 4 : Ajout de produits

Tout ce qui reste pour ce didacticiel est d’enregistrer les produits pour la base de données dans les produits à ajouter à partir du bouton expédition s `Click` Gestionnaire d’événements. Cela peut être accompli en créant un `ProductsDataTable` et en ajoutant un `ProductsRow` instance pour chacun des noms de produit fournies. Une fois ces `ProductsRow` s ont été ajoutées, nous permettront à un appel à la `ProductsBLL` classe s `UpdateWithTransaction` méthode en passant le `ProductsDataTable`. N’oubliez pas que le `UpdateWithTransaction` (méthode), qui a été créé dans le [encapsulant les Modifications de base de données d’une Transaction](wrapping-database-modifications-within-a-transaction-cs.md) passes (didacticiels), le `ProductsDataTable` à la `ProductsTableAdapter` s `UpdateWithTransaction` (méthode). À partir de là, une transaction ADO.NET est démarrée et les problèmes de TableAdatper un `INSERT` instruction à la base de données pour chaque ajouté `ProductsRow` dans la table de données. En supposant que tous les produits sont ajoutées sans erreur, que la transaction est validée, sinon elle est restaurée.

Le code pour ajouter des produits à partir du bouton expédition s `Click` Gestionnaire d’événements doit également effectuer un peu de vérification des erreurs. Dans la mesure où il n’y a aucune RequiredFieldValidators utilisé dans l’interface d’insertion, un utilisateur peut entrer un prix d’un produit lors de l’omission de son nom. Étant donné que le nom de produit s est obligatoire, si cette condition se déroule nous devons avertir l’utilisateur et de poursuivre les insertions. Le texte complet `Click` code gestionnaire d’événements suit :


[!code-csharp[Main](batch-inserting-cs/samples/sample6.cs)]

Le Gestionnaire d’événements démarre en s’assurant que les `Page.IsValid` propriété retourne une valeur de `true`. Si elle retourne `false`, puis qui signifie que l’un ou plus de la CompareValidators rapportent des données non valides ; dans ce cas nous ne souhaitons pas tenter d’insérer les produits entrés ou nous obtenez une exception lorsque vous tentez d’assigner le prix unitaire d’entré par l’utilisateur valeur à la `ProductsRow` s `UnitPrice` propriété.

Ensuite, une nouvelle `ProductsDataTable` instance est créée (`products`). A `for` boucle est utilisée pour itérer au sein du produit nom et le prix unitaire zones de texte et la `Text` propriétés sont lues dans les variables locales `productName` et `unitPrice`. Si l’utilisateur a entré une valeur pour le prix unitaire, mais pas pour le nom de produit correspondant, la `StatusLabel` affiche le message si vous fournissez une unité de prix que vous devez également inclure le nom du produit et le Gestionnaire d’événements est terminé.

Si un nom de produit a été fourni, une nouvelle `ProductsRow` instance est créée à l’aide de la `ProductsDataTable` s `NewProductsRow` (méthode). Cette nouvelle `ProductsRow` instance s `ProductName` est définie sur le produit actuel nom de zone de texte lors de la `SupplierID` et `CategoryID` sont les propriétés assignées à le `SelectedValue` propriétés des compréhension des listes dans l’en-tête de l’interface s insertion. Si l’utilisateur a entré une valeur pour le prix du produit s, il est assigné à la `ProductsRow` instance s `UnitPrice` propriété ; sinon, la propriété est gauche non assignée, ce qui entraîne un `NULL` valeur pour `UnitPrice` dans la base de données. Enfin, le `Discontinued` et `UnitsOnOrder` propriétés sont attribuées aux valeurs codées en dur `false` et 0, respectivement.

Une fois que les propriétés ont été attribuées à la `ProductsRow` instance, il est ajouté à la `ProductsDataTable`.

À la fin de la `for` boucle, nous vérifions si tous les produits ont été ajoutés. Ajouter des produits à partir d’expédition avant d’entrer les noms de produits ou le prix peut, après tout, avez cliqué à l’utilisateur. S’il existe au moins un produit dans le `ProductsDataTable`, le `ProductsBLL` classe s `UpdateWithTransaction` méthode est appelée. Ensuite, les données sont reliées à la `ProductsGrid` GridView afin que les produits nouvellement ajoutées seront affiche dans l’interface d’affichage. Le `StatusLabel` est mis à jour pour afficher un message de confirmation et le `ReturnToDisplayInterface` est appelé, le masquage de l’insertion d’interface et affichant l’interface d’affichage.

Si aucun produit ont été entrées, l’interface d’insertion reste affichée, mais le message Qu'aucun produit ont été ajoutés. Entrez les noms de produits et prix unitaire dans les zones de texte s’affiche.

Figure 13, 14 et 15 afficher l’insertion et affichent des interfaces dans l’action. Dans la Figure 13, l’utilisateur a entré une valeur du prix unitaire sans un nom de produit correspondant. La figure 14 illustre l’interface d’affichage après trois nouveaux produits ont été ajoutés avec succès, tandis que Figure 15 deux des produits nouvellement ajoutées s’affiche dans le GridView (le troisième est sur la page précédente).


[![Un nom de produit est requis lors de la saisie de prix unitaire](batch-inserting-cs/_static/image38.png)](batch-inserting-cs/_static/image37.png)

**Figure 13**: aucun nom de produit est requis lors de la saisie de prix unitaire ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image39.png))


[![Trois jardins nouveau ont été ajoutés pour le fournisseur Mayumi s](batch-inserting-cs/_static/image41.png)](batch-inserting-cs/_static/image40.png)

**La figure 14**: trois nouveaux jardins ont été ajoutés pour s fournisseur Mayumi ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image42.png))


[![Les nouveaux produits se trouvent dans la dernière Page du contrôle GridView](batch-inserting-cs/_static/image44.png)](batch-inserting-cs/_static/image43.png)

**Figure 15**: les nouveaux produits se trouvent dans la dernière Page du contrôle GridView ([cliquez pour afficher l’image en taille réelle](batch-inserting-cs/_static/image45.png))


> [!NOTE]
> Le lot d’insertion de logique utilisée dans ce didacticiel inclut les insertions dans l’étendue de transaction. Pour vérifier cela, délibérément présentent une erreur au niveau de la base de données. Par exemple, au lieu de l’affectation de la nouvelle `ProductsRow` instance s `CategoryID` valeur à la propriété sélectionnée dans le `Categories` DropDownList, affecter à une valeur comme `i * 5`. Ici `i` est l’indexeur de la boucle et a des valeurs comprises entre 1 et 5. Par conséquent, lorsque l’ajout de deux ou plusieurs produits de traitement par lots Insérer le premier produit aura valide `CategoryID` valeur (5), mais les produits suivants aura `CategoryID` les valeurs qui ne correspondent pas à `CategoryID` des valeurs dans le `Categories` table. Le résultat est que lors de la première `INSERT` réussit, les conditions suivantes échouent avec une violation de contrainte de clé étrangère. Étant donné que l’insertion de lot est atomique, la première `INSERT` sera restaurée, retour à la base de données à son état avant que le processus d’insertion de lot a commencé.


## <a name="summary"></a>Récapitulatif

Cet aspect ainsi que deux didacticiels précédents, nous avons créé les interfaces qui permettent de mettre à jour, suppression, et l’insertion des lots de données, tous les deux utilisés la prise en charge de transactions que nous avons ajouté à la couche d’accès aux données dans le [encapsulant les Modifications de base de données dans une Transaction](wrapping-database-modifications-within-a-transaction-cs.md) didacticiel. Pour certains scénarios, ces interfaces utilisateur pour le traitement par lots considérablement améliorent l’efficacité de l’utilisateur final en réduisant le nombre de clics, les publications (postback) et les commutateurs de contexte du clavier de la souris, tout en préservant l’intégrité des données sous-jacentes.

Ce didacticiel termine notre apparence à manipuler les données par lot. L’ensemble suivant de didacticiels explore un éventail de scénarios de couche d’accès aux données avancées, y compris à l’aide de procédures stockées dans les méthodes TableAdapter s, configuration des paramètres au niveau de la connexion et de la commande dans la couche DAL, le chiffrement des chaînes de connexion et bien plus encore !

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Responsable de réviseurs pour ce didacticiel ont été Hilton Giesenow et S ren Jacob Lauritsen. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](batch-deleting-cs.md)
[Suivant](wrapping-database-modifications-within-a-transaction-vb.md)
