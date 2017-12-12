---
uid: web-forms/overview/data-access/editing-inserting-and-deleting-data/handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb
title: La gestion des Exceptions au niveau de la couche BLL et de la couche DAL dans une Page ASP.NET (VB) | Documents Microsoft
author: rick-anderson
description: "Dans ce didacticiel, nous verrons comment afficher un message d’erreur convivial, informatif doit se produire une exception lors d’une insertion, mise à jour ou opération de suppression de..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/17/2006
ms.topic: article
ms.assetid: 129d4338-1315-4f40-89b5-2b84b807707d
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-inserting-and-deleting-data/handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb
msc.type: authoredcontent
ms.openlocfilehash: f5ebdf168610b715dc918ff6addf88d3c2a67097
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="handling-bll--and-dal-level-exceptions-in-an-aspnet-page-vb"></a>La gestion des Exceptions au niveau de la couche BLL et de la couche DAL dans une Page ASP.NET (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_18_VB.exe) ou [télécharger le PDF](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/datatutorial18vb1.pdf)

> Dans ce didacticiel, nous verrons comment afficher un message d’erreur convivial, informatif doit se produire une exception lors d’une insertion, mise à jour ou opération de suppression d’un contrôle Web de données ASP.NET.


## <a name="introduction"></a>Introduction

Utilisation des données à partir de l’application web ASP.NET à l’aide d’une architecture d’application à plusieurs niveaux implique trois étapes générales suivantes :

1. Déterminer quelle méthode de la couche de logique métier doit être appelé et le paramètre des valeurs pour la passer. Les valeurs de paramètre peuvent être codé en dur, assignée par programme, ou entrées entré par l’utilisateur.
2. Appelez la méthode.
3. Traiter les résultats. Lorsque vous appelez une méthode de la couche BLL qui retourne des données, cela peut impliquer la liaison des données à un contrôle Web de données. Pour les méthodes BLL qui modifient les données, cela peut inclure l’exécution d’une action basée sur une valeur de retour ou en douceur gère toute exception qui a été créée à l’étape 2.

Comme nous l’avons vu dans la [didacticiel précédent](examining-the-events-associated-with-inserting-updating-and-deleting-vb.md), ObjectDataSource et les contrôles Web de données fournissent des points d’extensibilité pour les étapes 1 et 3. Le GridView, par exemple, déclenche son `RowUpdating` événement avant de pouvoir affecter ses valeurs de champ à son ObjectDataSource `UpdateParameters` collection ; sa `RowUpdated` événement est déclenché après l’ObjectDataSource a terminé l’opération.

Nous avons déjà examiné les événements qui déclenchent au cours de l’étape 1 et savez comment ils peuvent être utilisés pour personnaliser les paramètres d’entrée ou d’annuler l’opération. Dans ce didacticiel, nous allons notre attention sur les événements qui déclenchent une fois l’opération terminée. Avec ces gestionnaires d’événements post-niveau que pouvez, entre autres choses, déterminer si une exception s’est produite lors de l’opération et gérer de façon appropriée, afficher un message d’erreur convivial, information sur l’écran plutôt que par défaut pour ASP.NET standard page de l’exception.

Pour illustrer l’utilisation de ces événements post-niveau, nous allons créer une page qui répertorie les produits dans un GridView modifiable. Lorsque la mise à jour d’un produit, si une exception est levée notre ASP.NET page affiche un bref message ci-dessus le GridView qui explique qu’un problème s’est produite. C’est parti !

## <a name="step-1-creating-an-editable-gridview-of-products"></a>Étape 1 : Création d’un GridView modifiable de produits

Dans le didacticiel précédent, nous avons créé un GridView modifiable avec seulement deux champs, `ProductName` et `UnitPrice`. Cela est requis la création d’une surcharge supplémentaire pour le `ProductsBLL` la classe `UpdateProduct` (méthode), celui qui acceptées uniquement trois paramètres d’entrée (nom du produit, le prix unitaire et ID) par opposition un paramètre pour chaque champ de produit. Pour ce didacticiel, nous allons vous exercer cette technique, la création d’un contrôle GridView modifiable qui affiche le nom du produit, quantité par unité, le prix unitaire et des unités en stock, mais autorise uniquement le nom, le prix unitaire et des unités en stock à modifier.

Pour prendre en charge ce scénario, nous aurons besoin une autre surcharge de la `UpdateProduct` méthode, un constructeur qui accepte quatre paramètres : le nom du produit, le prix unitaire, les unités en stock et de code. Ajoutez la méthode suivante à la `ProductsBLL` classe :


[!code-vb[Main](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/samples/sample1.vb)]

Cette méthode est terminée, nous sommes prêts à créer la page ASP.NET qui permet la modification de ces quatre champs produit particulier de. Ouvrez le `ErrorHandling.aspx` page dans le `EditInsertDelete` dossier et ajouter un contrôle GridView à la page via le concepteur. Lier le contrôle GridView à une nouvelle ObjectDataSource, mappage la `Select()` méthode à la `ProductsBLL` la classe `GetProducts()` (méthode) et le `Update()` méthode à la `UpdateProduct` surcharge venez de créer.


[![Utilisez la surcharge de méthode de UpdateProduct qui accepte quatre paramètres d’entrée](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image2.png)](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image1.png)

**Figure 1**: utilisez le `UpdateProduct` méthode surcharge qu’accepte quatre paramètres d’entrée ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image3.png))


Cette opération crée un ObjectDataSource avec un `UpdateParameters` collection avec quatre paramètres et un GridView avec un champ pour chacun des champs de produit. Balisage déclaratif de l’ObjectDataSource affecte le `OldValuesParameterFormatString` la valeur de la propriété `original_{0}`, ce qui entraîne une exception, car la classe de notre BLL n’en attendez pas un paramètre d’entrée nommé `original_productID` à passer dans. N’oubliez pas de supprimer ce paramètre complètement à partir de la syntaxe déclarative (ou affectez-lui la valeur par défaut, `{0}`).

Ensuite, alléger le contrôle GridView à inclure uniquement les `ProductName`, `QuantityPerUnit`, `UnitPrice`, et `UnitsInStock` BoundFields. N’hésitez pas à appliquer au niveau du champ mise en forme vous jugez nécessaires (telles que la modification du `HeaderText` propriétés).

Dans le didacticiel précédent, nous avons à la mise en forme le `UnitPrice` BoundField sous forme de devise à la fois en mode lecture seule et en mode édition. Nous allons suivre le même ici. Rappelez-vous que cela nécessite la définition de la BoundField `DataFormatString` propriété `{0:c}`, ses `HtmlEncode` propriété `false`et son `ApplyFormatInEditMode` à `true`, comme illustré dans la Figure 2.


[![Configurer le prix unitaire BoundField à afficher sous forme de devise](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image5.png)](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image4.png)

**Figure 2**: configurer le `UnitPrice` BoundField à afficher sous forme de devise ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image6.png))


Mise en forme le `UnitPrice` comme une devise dans l’interface de modification nécessite la création d’un gestionnaire d’événements pour le contrôle du GridView `RowUpdating` événement qui analyse la chaîne au format monétaire dans un `decimal` valeur. N’oubliez pas que le `RowUpdating` Gestionnaire d’événements à partir du dernier didacticiel également vérifiés pour s’assurer que l’utilisateur fourni un `UnitPrice` valeur. Toutefois, pour ce didacticiel, nous allons autoriser l’utilisateur à omettre le prix.


[!code-vb[Main](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/samples/sample2.vb)]

Notre GridView inclut un `QuantityPerUnit` BoundField, mais cette BoundField doit être uniquement à des fins d’affichage et ne doit pas être modifiées par l’utilisateur. Pour ce faire, il suffit de définir la BoundFields' `ReadOnly` propriété `true`.


[![Définir le QuantityPerUnit BoundField en lecture seule](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image8.png)](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image7.png)

**Figure 3**: Vérifiez la `QuantityPerUnit` BoundField en lecture seule ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image9.png))


Enfin, vérifiez la case à cocher Activer la modification de la balise active du GridView. Après avoir effectué ces étapes le `ErrorHandling.aspx` le Concepteur de la page doit ressembler à la Figure 4.


[![Supprimer tous les dossiers sauf nécessaire BoundFields et vérifiez l’activer la case à cocher de modification](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image11.png)](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image10.png)

**Figure 4**: supprimez tout le nécessaire BoundFields et cocher la case Activer la modification ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image12.png))


À ce stade, nous avons une liste de tous les produits `ProductName`, `QuantityPerUnit`, `UnitPrice`, et `UnitsInStock` champs ; Toutefois, seuls le `ProductName`, `UnitPrice`, et `UnitsInStock` champs peuvent être modifiés.


[![Les utilisateurs peuvent désormais facilement modifier les noms des produits, les prix et les unités en Stock champs](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image14.png)](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image13.png)

**Figure 5**: les utilisateurs peuvent désormais facilement modifier produits noms prix et champs unités en Stock ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image15.png))


## <a name="step-2-gracefully-handling-dal-level-exceptions"></a>Étape 2 : Gestion normalement des Exceptions au niveau de la couche DAL

Bien que notre GridView modifiable fonctionne très lorsque les utilisateurs entrent des valeurs autorisées pour le nom du produit modifié, les prix et les unités en stock, entrer des valeurs illégales entraîne une exception. Par exemple, si vous omettez le `ProductName` valeur entraîne une [NoNullAllowedException](https://msdn.microsoft.com/library/default.asp?url=/library/en-us/cpref/html/frlrfsystemdatanonullallowedexceptionclasstopic.asp) levée depuis le `ProductName` propriété dans le `ProdcutsRow` classe a son `AllowDBNull` propriété la valeur `false`; si la base de données est arrêté, un `SqlException` seront levées par le TableAdapter lorsque vous tentez de vous connecter à la base de données. Sans effectuer aucune action, ces exceptions se propagent de la couche d’accès aux données à la couche de logique métier, puis dans la page ASP.NET et enfin à l’exécution d’ASP.NET.

En fonction de la configuration de votre application web et ou non que vous visitez l’application à partir de `localhost`, une exception non gérée peut entraîner une page d’erreur de serveur générique, un rapport d’erreur détaillé ou une page web conviviale. Consultez [Web Application une gestion d’erreurs dans ASP.NET](http://www.15seconds.com/issue/030102.htm) et [customErrors, élément](https://msdn.microsoft.com/en-US/library/h0hfz6fc(VS.80).aspx) pour plus d’informations sur la façon dont le runtime ASP.NET répond à une exception non interceptée.

La figure 6 illustre l’écran rencontré lors de la tentative de mise à jour d’un produit sans spécifier le `ProductName` valeur. Ceci est la valeur par défaut, le rapport d’erreurs détaillées affiché lorsque provenant de `localhost`.


[![L’omission de détails de l’Exception affichage nom du produit](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image17.png)](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image16.png)

**Figure 6**: l’omission nom sera affichage Détails du produit de l’Exception ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image18.png))


Si ces détails de l’exception sont utiles lorsque vous testez une application, présentant un utilisateur final d’écran en cas d’une exception est loin d’être idéal. Un utilisateur final probable ne sait pas ce un `NoNullAllowedException` est ou de la raison pour laquelle elle a été générée. Une meilleure approche consiste à l’utilisateur avec un message plus convivial expliquant que pose tente de mettre à jour le produit.

Si une exception se produit lors de l’exécution de l’opération, les événements post-niveau dans ObjectDataSource et le contrôle de données Web fournissent un moyen de détecter et d’annuler l’exception de se propager à l’exécution d’ASP.NET. Dans notre exemple, nous allons créer un gestionnaire d’événements pour le contrôle du GridView `RowUpdated` événement qui détermine si une exception a été déclenchée et, dans ce cas, affiche les détails de l’exception dans un contrôle Web Label.

Commencez par ajouter une étiquette à la page ASP.NET, en définissant ses `ID` propriété `ExceptionDetails` et supprimant sa `Text` propriété. Pour dessiner des yeux de l’utilisateur à ce message, définissez son `CssClass` propriété `Warning`, qui est une classe CSS que nous avons ajouté à la `Styles.css` fichier dans le didacticiel précédent. Rappelez-vous que cette classe CSS provoque le texte de l’étiquette à afficher dans une police rouge, gras, italique très grande taille.


[![Ajouter un contrôle Web d’étiquette à la Page](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image20.png)](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image19.png)

**Figure 7**: ajouter un contrôle Web d’étiquette à la Page ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image21.png))


Comme nous voulons que ce contrôle Web Label soit visible uniquement immédiatement après une exception s’est produite, définissez son `Visible` propriété sur false dans le `Page_Load` Gestionnaire d’événements :


[!code-vb[Main](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/samples/sample3.vb)]

Avec ce code, sur la première visite de page et les publications ultérieures le `ExceptionDetails` contrôle aura son `Visible` propriété `false`. En cas d’une exception au niveau de la couche DAL ou de la couche BLL, ce qui nous pouvons détecter dans le GridView `RowUpdated` Gestionnaire d’événements, nous allons définir le `ExceptionDetails` du contrôle `Visible` true à la propriété. Étant donné que les gestionnaires d’événements de contrôle Web se produisent après le `Page_Load` Gestionnaire d’événements dans le cycle de vie de page, l’étiquette s’affiche. Toutefois, sur la publication suivante, le `Page_Load` reviendra de gestionnaire d’événements le `Visible` propriété revenir `false`, masquer à nouveau à partir de la vue.

> [!NOTE]
> Ou bien, nous pouvons éliminer la nécessité pour le paramètre la `ExceptionDetails` du contrôle `Visible` propriété dans `Page_Load` en attribuant son `Visible` propriété `false` dans la syntaxe déclarative et la désactivation de son état d’affichage (définissant son `EnableViewState` propriété `false`). Nous allons utiliser cette approche alternative dans un didacticiel futures.


Avec le contrôle Label est ajouté, est l’étape suivante pour créer le Gestionnaire d’événements pour le contrôle du GridView `RowUpdated` événement. Sélectionnez le contrôle GridView dans le concepteur, accédez à la fenêtre Propriétés, cliquez sur l’icône d’éclair, qui répertorie les événements de GridView. Il doit déjà être il d’une entrée pour le contrôle du GridView `RowUpdating` événement, comme nous avons créé un gestionnaire d’événements pour cet événement plus haut dans ce didacticiel. Créer un gestionnaire d’événements pour le `RowUpdated` également des événements.


![Créer un gestionnaire d’événements pour l’événement RowUpdated de GridView](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image22.png)

**Figure 8**: créer un gestionnaire d’événements pour le contrôle du GridView `RowUpdated` événement


> [!NOTE]
> Vous pouvez également créer le Gestionnaire d’événements via les listes déroulantes en haut du fichier de classe code-behind. Sélectionnez le contrôle GridView à partir de la liste déroulante à gauche et le `RowUpdated` événement à partir de celui situé à droite.


Création de ce gestionnaire d’événements ajoute le code suivant à la classe code-behind de la page ASP.NET :


[!code-vb[Main](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/samples/sample4.vb)]

Second paramètre de ce gestionnaire d’événements est un objet de type [GridViewUpdatedEventArgs](https://msdn.microsoft.com/en-US/library/system.web.ui.webcontrols.gridviewupdatedeventargs.aspx), qui a trois propriétés dignes d’intérêt pour la gestion des exceptions :

- `Exception`une référence à l’exception levée ; Si aucune exception n’a été levée, cette propriété a une valeur`null`
- `ExceptionHandled`valeur booléenne qui indique si l’exception a été gérée dans le `RowUpdated` Gestionnaire d’événements ; si `false` (la valeur par défaut), l’exception est à nouveau levée, percolation jusqu'à l’exécution d’ASP.NET
- `KeepInEditMode`Si la valeur `true` la ligne GridView modifiée reste en mode édition ; si `false` (la valeur par défaut), la ligne GridView revienne à son mode de lecture seule

Notre code, puis vérifiez si `Exception` n’est pas `null`, ce qui signifie que qu’une exception a été levée pendant l’opération. Si c’est le cas, nous voulons :

- Affichez un message convivial dans le `ExceptionDetails` étiquette
- Indiquer que l’exception a été gérée.
- Conserver la ligne GridView en mode édition

Le code suivant effectue ces objectifs :


[!code-vb[Main](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/samples/sample5.vb)]

Ce gestionnaire d’événements commence par vérifier si `e.Exception` est `null`. S’il n’est pas le cas, le `ExceptionDetails` l’étiquette `Visible` est définie sur `true` et son `Text` propriété « S’est un problème de mise à jour du produit. » Les détails de l’exception réelle qui a été levée résident dans le `e.Exception` l’objet `InnerException` propriété. Cette exception interne est vérifiée et, s’il s’agit d’un type particulier, un message supplémentaire, utile est ajouté à la `ExceptionDetails` l’étiquette `Text` propriété. Enfin, le `ExceptionHandled` et `KeepInEditMode` propriétés sont définies sur `true`.

La figure 9 illustre une capture d’écran de cette page lors de l’omission du nom du produit ; La figure 10 illustre les résultats lorsque vous entrez une instruction non conforme `UnitPrice` valeur (-50).


[![Le ProductName BoundField doit contenir une valeur](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image24.png)](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image23.png)

**Figure 9**: le `ProductName` BoundField doit contenir une valeur ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image25.png))


[![Les valeurs négatives UnitPrice sont pas autorisées](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image27.png)](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image26.png)

**La figure 10**: négatif `UnitPrice` les valeurs sont pas autorisés ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image28.png))


En définissant le `e.ExceptionHandled` propriété `true`, le `RowUpdated` Gestionnaire d’événements a indiqué qu’il a géré l’exception. Par conséquent, l’exception ne se propage jusqu'à l’exécution d’ASP.NET.

> [!NOTE]
> Figures 9 et 10 montrent un moyen approprié pour gérer les exceptions levées en raison d’une entrée d’utilisateur non valide. Dans l’idéal, cependant, ce type d’entrée non valide ne peut pas atteindre la couche de logique métier en premier lieu, comme la page ASP.NET doit s’assurer que les entrées de l’utilisateur sont valides avant d’appeler le `ProductsBLL` la classe `UpdateProduct` (méthode). Dans notre didacticiel suivant, que nous verrons comment ajouter des contrôles de validation pour les interfaces de modification et d’insertion pour vous assurer que les données envoyées à la couche de logique métier est conforme aux règles d’entreprise. Les contrôles de validation n'empêchent pas seulement l’appel de la `UpdateProduct` méthode jusqu'à ce que les données fournies par l’utilisateur sont valides, mais également fournissent une expérience utilisateur plus explicites pour l’identification des problèmes de saisie de données.


## <a name="step-3-gracefully-handling-bll-level-exceptions"></a>Étape 3 : Traitement normalement des Exceptions au niveau de la couche BLL

Lors de l’insertion, mise à jour ou supprimer des données, la couche d’accès aux données peut lever une exception en cas d’une erreur liée aux données. La base de données est peut-être hors connexion, une colonne de table de base de données requise aviez ne peut-être pas une valeur spécifiée ou une contrainte de niveau table a été violée. En plus des exceptions strictement liées aux données, la couche de logique métier pouvez utiliser des exceptions pour indiquer les cas de non-respect des règles d’entreprise. Dans le [création d’une couche de logique métier](../introduction/creating-a-business-logic-layer-vb.md) (didacticiel), par exemple, nous avons ajouté une vérification de règle d’entreprise à l’original `UpdateProduct` de surcharge. Plus précisément, si l’utilisateur a été marquant un produit comme étant suspendus, nous requis que le produit doit ne pas être la seule fournie par son fournisseur. Si cette condition a été violée, un `ApplicationException` a été levée.

Pour le `UpdateProduct` surcharge créé dans ce didacticiel, vous allez ajouter une règle d’entreprise qui interdit le `UnitPrice` champ définie sur une nouvelle valeur qui est supérieur au double de l’original `UnitPrice` valeur. Pour ce faire, vous devez ajuster le `UpdateProduct` surcharge afin qu’il effectue cette vérification et lève un `ApplicationException` si la règle est violée. La méthode de mise à jour suivante :


[!code-vb[Main](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/samples/sample6.vb)]

Avec cette modification, toute mise à jour de prix est supérieur à deux fois le prix existant entraîne une `ApplicationException` levée. Tout comme l’exception levée à partir de la couche DAL, ce déclenché par couche BLL `ApplicationException` peuvent être détectés et gérés dans le GridView `RowUpdated` Gestionnaire d’événements. En fait, le `RowUpdated` code du Gestionnaire d’événements lors de l’écriture, correctement détecte cette exception et afficher le `ApplicationException`de `Message` valeur de propriété. La figure 11 illustre un capture d’écran lors un utilisateur tente de mettre à jour le prix de Tran à 50,00 $, qui est supérieure à deux fois son prix courant de 19,95 $.


[![Les règles d’entreprise interdire les augmentations de prix que plus de deux fois le prix d’un produit](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image30.png)](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image29.png)

**Figure 11**: interdire les augmentations de prix plus de deux fois le prix d’un produit des règles d’entreprise ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb/_static/image31.png))


> [!NOTE]
> Dans l’idéal seraient de refactoriser nos règles de logique métier de le `UpdateProduct` surcharges de méthode dans une méthode courante. Cela est considérée comme un exercice pour le lecteur.


## <a name="summary"></a>Résumé

Pendant l’insertion, mise à jour et la suppression des opérations, à la fois le données Web et le contrôle ObjectDataSource impliqués déclenchent les événements antérieurs et postérieurs au niveau que l’opération réelle serre-livres. Comme nous l’avons vu dans ce didacticiel et le précédent, lorsque vous travaillez avec un GridView modifiable du GridView `RowUpdating` événement se déclenche, suivie de l’ObjectDataSource `Updating` événement, à partir de laquelle la commande de mise à jour est effectuée à l’ObjectDataSource objet sous-jacent. Une fois l’opération terminée, l’ObjectDataSource `Updated` se déclenche l’événement, suivi du GridView `RowUpdated` événement.

Nous pouvons créer des gestionnaires d’événements pour les événements de niveau préalable afin de personnaliser les paramètres d’entrée ou pour les événements post-niveau pour inspecter et répondre aux résultats de l’opération. Gestionnaires d’événements post-niveau sont couramment utilisés pour détecter si une exception s’est produite lors de l’opération. En cas d’une exception, ces gestionnaires d’événements post-niveau peuvent gérer éventuellement de l’exception sur leurs propres. Dans ce didacticiel, nous avons vu comment gérer cette exception en affichant un message d’erreur convivial.

Dans l’étape suivante du didacticiel, nous verrons comment réduire la probabilité d’exceptions provenant de problèmes de mise en forme des données (par exemple, entrez une valeur négative `UnitPrice`). Plus précisément, nous allons examiner comment ajouter des contrôles de validation pour les interfaces de modification et d’insertion.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Liz Shulok. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](examining-the-events-associated-with-inserting-updating-and-deleting-vb.md)
[Suivant](adding-validation-controls-to-the-editing-and-inserting-interfaces-vb.md)
