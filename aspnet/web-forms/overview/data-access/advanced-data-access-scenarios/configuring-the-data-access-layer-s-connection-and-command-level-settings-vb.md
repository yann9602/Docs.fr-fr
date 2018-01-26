---
uid: web-forms/overview/data-access/advanced-data-access-scenarios/configuring-the-data-access-layer-s-connection-and-command-level-settings-vb
title: "Configuration des paramètres de niveau de connexion et de commande de la couche d’accès aux données (VB) | Documents Microsoft"
author: rick-anderson
description: "Les TableAdapters dans un DataSet typé prend automatiquement en charge de la connexion à la base de données, émettre des commandes et remplir un DataTable avec les résultats..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/03/2007
ms.topic: article
ms.assetid: d57dfa2b-d627-45cb-b5b1-abbf3159d770
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/advanced-data-access-scenarios/configuring-the-data-access-layer-s-connection-and-command-level-settings-vb
msc.type: authoredcontent
ms.openlocfilehash: ab392f2a7d9b6cf97da920f899aea23379209f96
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="configuring-the-data-access-layers-connection--and-command-level-settings-vb"></a>Configuration des paramètres de niveau de connexion et de commande de la couche d’accès aux données (Visual Basic)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_72_VB.zip) ou [télécharger le PDF](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/_static/datatutorial72vb1.pdf)

> Les TableAdapters dans un DataSet typé prend automatiquement en charge de la connexion à la base de données, émettre des commandes et remplir un DataTable avec les résultats. Il sont reprises quand nous souhaitons prendre en charge de ces informations nous et dans ce didacticiel vous apprendre comment accéder aux paramètres de base de données au niveau de la connexion et de la commande dans le TableAdapter.


## <a name="introduction"></a>Introduction

Dans l’ensemble de la série de didacticiels, nous avons utilisé typés pour implémenter la couche d’accès aux données et des objets métier de notre architecture multiniveau. Comme indiqué dans le [premier didacticiel](../introduction/creating-a-data-access-layer-vb.md), s DataSet typé DataTables servir en tant que référentiels de données alors que les TableAdapters agissent comme des wrappers pour communiquer avec la base de données pour extraire et modifier les données sous-jacentes. Les TableAdapters encapsuler la complexité de l’utilisation de la base de données et nous évite de devoir écrire du code pour se connecter à la base de données, d’émettre une commande ou de remplir les résultats dans un DataTable.

Il existe des cas, toutefois, lorsque nous aurons besoin pour écrire du code qui fonctionne directement avec les objets ADO.NET et les procédures dans les profondeurs du TableAdapter. Dans le [encapsulant les Modifications de base de données d’une Transaction](../working-with-batched-data/wrapping-database-modifications-within-a-transaction-vb.md) (didacticiel), par exemple, nous avons ajouté méthodes au TableAdapter de début, la validation et annulation de transactions d’ADO.NET. Ces méthodes utilisées en interne, créés manuellement `SqlTransaction` objet qui a été assigné au TableAdapter s `SqlCommand` objets.

Dans ce didacticiel, nous allons examiner comment accéder aux paramètres de base de données au niveau de la connexion et de la commande dans le TableAdapter. En particulier, nous allons ajouter des fonctionnalités à la `ProductsTableAdapter` que permet l’accès à la chaîne de connexion sous-jacent et les paramètres de délai d’attente de la commande.

## <a name="working-with-data-using-adonet"></a>Utilisation des données à l’aide d’ADO.NET

Microsoft .NET Framework contient une multitude de classes conçues spécifiquement pour fonctionner avec des données. Ces classes, trouvés dans le [ `System.Data` espace de noms](https://msdn.microsoft.com/library/system.data.aspx), sont appelés les *ADO.NET* classes. Certaines des classes dans le cadre ADO.NET sont liés à un particulier *fournisseur de données*. Vous pouvez considérer un fournisseur de données comme un canal de communication qui permet des informations entre les classes ADO.NET et le magasin de données sous-jacent. Il existe des fournisseurs généralisés, telles qu’OLE DB et ODBC, ainsi que les fournisseurs qui sont spécialement conçus pour un système de base de données particulière. Par exemple, s’il est possible de se connecter à une base de données Microsoft SQL Server à l’aide du fournisseur OleDb, le fournisseur SqlClient est beaucoup plus efficace car elle a été conçu et optimisé spécifiquement pour SQL Server.

Lors de l’accès par programme aux données, le modèle suivant est couramment utilisé :

1. Établir une connexion à la base de données.
2. Émettre une commande.
3. Pour `SELECT` requêtes, travailler avec les enregistrements résultants.

Il existe des classes ADO.NET distinctes pour la réalisation de chacune de ces étapes. Pour vous connecter à une base de données à l’aide du fournisseur SqlClient, par exemple, utiliser le [ `SqlConnection` classe](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(VS.80).aspx). Pour émettre un `INSERT`, `UPDATE`, `DELETE`, ou `SELECT` commande à la base de données, utilisez la [ `SqlCommand` classe](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx).

À l’exception de la [encapsulant les Modifications de base de données d’une Transaction](../working-with-batched-data/wrapping-database-modifications-within-a-transaction-vb.md) didacticiel, nous n’avons pas reçu les écrire à ADO.NET de bas niveau nous-mêmes de code, car le code généré automatiquement des TableAdapters inclut la fonctionnalité nécessaire pour se connecter à la base de données, exécuter des commandes, récupérer des données et remplir ces données dans les tables de données. Toutefois, il peut arriver lorsque nous aurons besoin de personnaliser ces paramètres de bas niveau. Sur les étapes suivantes, nous allons examiner comment exploiter les objets ADO.NET utilisés en interne par les TableAdapters.

## <a name="step-1-examining-with-the-connection-property"></a>Étape 1 : Examen avec la propriété de connexion

Chaque classe de TableAdapter a un `Connection` propriété qui spécifie les informations de connexion de base de données. Ce type de données de propriété s et `ConnectionString` valeur sont déterminées par les sélections effectuées dans l’Assistant Configuration de TableAdapter. Rappelez-vous que lorsque nous d’abord ajouter un TableAdapter à un DataSet typé cet Assistant vous demande us pour la base de données source (voir Figure 1). La liste déroulante dans cette première étape inclut les bases de données spécifiées dans le fichier de configuration, ainsi que toutes les autres bases de données dans l’Explorateur de serveurs s les connexions de données. Si vous souhaitez utiliser la base de données n’existe pas dans la liste déroulante, vous pouvez spécifier une nouvelle connexion de base de données en cliquant sur le bouton Nouvelle connexion et en fournissant les informations de connexion nécessaires.


[![La première étape de l’Assistant Configuration de TableAdapter](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/_static/image2.png)](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/_static/image1.png)

**Figure 1**: la première étape de l’Assistant Configuration de TableAdapter ([cliquez pour afficher l’image en taille réelle](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/_static/image3.png))


S permettent de prendre quelques instants pour inspecter le code pour le TableAdapter s `Connection` propriété. Comme indiqué dans le [création d’une couche d’accès aux données](../introduction/creating-a-data-access-layer-vb.md) didacticiel, nous pouvons afficher le code du TableAdapter généré automatiquement en accédant à la fenêtre Affichage de classes, parcours de la classe appropriée, puis double-cliquez sur le nom du membre.

Accédez à la fenêtre Affichage de classes en allant dans le menu Affichage et en choisissant d’affichage de classes (ou en tapant Ctrl + Maj + C). Dans la moitié supérieure de la fenêtre Affichage de classes, accéder à la `NorthwindTableAdapters` espace de noms et sélectionnez le `ProductsTableAdapter` classe. Cette action affiche le `ProductsTableAdapter` membres s dans le bas la moitié de l’affichage de classes, comme indiqué dans la Figure 2. Double-cliquez sur le `Connection` propriété pour connaître son code.


![Double-cliquez sur la propriété de connexion dans l’affichage de classes pour afficher son Code généré automatiquement](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/_static/image4.png)

**Figure 2**: double-cliquez sur la propriété de connexion dans l’affichage de classes pour afficher son Code généré automatiquement


Le TableAdapter s `Connection` propriété et autres suit code lié à la connexion :


[!code-vb[Main](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/samples/sample1.vb)]

Lorsque la classe du TableAdapter est instanciée, la variable membre `_connection` est égal à `Nothing`. Lorsque le `Connection` propriété est accessible, il vérifie si le `_connection` variable membre a été instanciée. Si elle n’a pas, le `InitConnection` méthode est appelée, ce qui instancie `_connection` et définit ses `ConnectionString` propriété à la valeur de chaîne de connexion spécifiée à partir de l’étape de première Configuration de TableAdapter Assistant s.

Le `Connection` propriété peut également être affectée à un `SqlConnection` objet. Cela associe le nouveau `SqlConnection` objet avec chacun des s TableAdapter `SqlCommand` objets.

## <a name="step-2-exposing-connection-level-settings"></a>Étape 2 : Exposer les paramètres de niveau connexion

Les informations de connexion doivent rester encapsulées dans le TableAdapter et ne pas être accessibles à d’autres couches de l’architecture d’application. Toutefois, il peut être des scénarios lorsque les informations de connexion au niveau de TableAdapter s doivent être accessible ou personnalisables pour une requête, un utilisateur ou une page ASP.NET.

S permettent d’étendre le `ProductsTableAdapter` dans les `Northwind` DataSet à inclure un `ConnectionString` propriété qui peut être utilisée par la couche de logique métier pour lire ou modifier la chaîne de connexion utilisée par le TableAdapter.

> [!NOTE]
> A *chaîne de connexion* est une chaîne qui spécifie les informations de connexion de base de données, tel que le fournisseur à utiliser, l’emplacement de la base de données, les informations d’identification de l’authentification et les autres paramètres liés à la base de données. Pour obtenir la liste des modèles de chaîne de connexion utilisée par un grand nombre de fournisseurs et de magasins de données, consultez [ConnectionStrings.com](http://www.connectionstrings.com/).


Comme indiqué dans le [création d’une couche d’accès aux données](../introduction/creating-a-data-access-layer-vb.md) didacticiel, les classes générées automatiquement de DataSet typé s peuvent être étendus via l’utilisation des classes partielles. Tout d’abord, créez un nouveau sous-dossier dans le projet nommé `ConnectionAndCommandSettings` sous le `~/App_Code/DAL` dossier.


![Ajouter un sous-dossier nommé ConnectionAndCommandSettings](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/_static/image5.png)

**Figure 3**: ajouter un sous-dossier nommé`ConnectionAndCommandSettings`


Ajouter un nouveau fichier de classe nommé `ProductsTableAdapter.ConnectionAndCommandSettings.vb` et entrez le code suivant :


[!code-vb[Main](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/samples/sample2.vb)]

Cette classe partielle ajoute un `Public` propriété nommée `ConnectionString` à la `ProductsTableAdapter` classe qui permet à toutes les couches lire ou mettre à jour la chaîne de connexion pour la connexion sous-jacente TableAdapter s.

Avec cette classe partielle créé (et enregistré), ouvrez le `ProductsBLL` classe. À une des méthodes existantes, tapez `Adapter` , puis appuyez sur la touche de point pour afficher IntelliSense. Vous devez voir le nouveau `ConnectionString` disponibles dans IntelliSense, ce qui signifie que vous pouvez par programmation lire ou modifier cette valeur à partir de la couche BLL de propriété.

## <a name="exposing-the-entire-connection-object"></a>Exposition de l’objet de connexion entière

Cette classe partielle expose qu’une des propriétés de l’objet de connexion sous-jacent : `ConnectionString`. Si vous souhaitez libérer de l’objet de connexion entière au-delà des limites du TableAdapter, vous pouvez également modifier le `Connection` au niveau de protection de la propriété s. Le code généré automatiquement, nous avons examiné à l’étape 1 a montré que le TableAdapter s `Connection` propriété est marquée comme étant `Friend`, c'est-à-dire que cette application soit uniquement accessible par les classes dans le même assembly. Cela peut être modifié, toutefois, via le TableAdapter s `ConnectionModifier` propriété.

Ouvrez le `Northwind` jeu de données, cliquez sur le `ProductsTableAdatper` dans le concepteur et accédez à la fenêtre Propriétés. Vous voyez le `ConnectionModifier` défini à sa valeur par défaut, `Assembly`. Pour rendre le `Connection` disponible en dehors de l’assembly de s DataSet typé, modification de la propriété du `ConnectionModifier` propriété `Public`.


[![Le niveau d’accessibilité de connexion propriété s peut être configuré via la propriété ConnectionModifier](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/_static/image7.png)](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/_static/image6.png)

**Figure 4**: le `Connection` propriété s accessibilité niveau peut être configuré via la `ConnectionModifier` propriété ([cliquez pour afficher l’image en taille réelle](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/_static/image8.png))


Enregistrer le jeu de données, puis revenez à la `ProductsBLL` classe. Comme avant, accédez à une des méthodes existantes et tapez `Adapter` , puis appuyez sur la touche de point pour afficher IntelliSense. La liste doit inclure un `Connection` propriété, ce qui signifie que vous pouvez désormais par programmation lire ou affecter tous les paramètres de connexion au niveau de la couche BLL.

## <a name="step-3-examining-the-command-related-properties"></a>Étape 3 : Examiner les propriétés de commande

Un TableAdapter se compose d’une requête principale, qui, par défaut, a généré automatiquement `INSERT`, `UPDATE`, et `DELETE` instructions. Cette requête principale s `INSERT`, `UPDATE`, et `DELETE` instructions sont implémentées dans le code du TableAdapter s comme un objet d’adaptateur de données ADO.NET via le `Adapter` propriété. Par exemple lors de sa `Connection` propriété, le `Adapter` type de données de propriété s est déterminé par le fournisseur de données utilisé. Étant donné que ces didacticiels utilisent le fournisseur SqlClient, le `Adapter` propriété est de type [ `SqlDataAdapter` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqldataadapter(VS.80).aspx).

Le TableAdapter s `Adapter` propriété possède trois propriétés de type `SqlCommand` qu’il utilise pour problème la `INSERT`, `UPDATE`, et `DELETE` instructions :

- `InsertCommand`
- `UpdateCommand`
- `DeleteCommand`

A `SqlCommand` objet est chargé d’envoyer une requête spécifique à la base de données et possède des propriétés comme : [ `CommandText` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.commandtext.aspx), qui contient l’instruction SQL d’ad hoc ou procédure stockée à exécuter ; et [ `Parameters` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.parameters.aspx), qui est une collection de `SqlParameter` objets. Comme nous l’avons vu dans la [création d’une couche d’accès aux données](../introduction/creating-a-data-access-layer-vb.md) (didacticiel), ces commandes objets peuvent être personnalisés via la fenêtre Propriétés.

En plus de la requête principale, le TableAdapter peut inclure un nombre variable de méthodes qui, lorsqu’elle est appelée, une commande spécifiée pour la base de données de distribution. L’objet de commande de requête principale s et les objets de commande pour toutes les méthodes supplémentaires sont stockés dans le TableAdapter s `CommandCollection` propriété.

S permettent de prendre un moment pour examiner le code généré par le `ProductsTableAdapter` dans le `Northwind` DataSet pour ces deux propriétés et leurs méthodes d’assistance et la prise en charge les variables de membre :


[!code-vb[Main](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/samples/sample3.vb)]

Le code pour le `Adapter` et `CommandCollection` propriétés reproduit étroitement celle de la `Connection` propriété. Il existe des variables de membre qui contiennent les objets utilisés par les propriétés. Les propriétés `Get` accesseurs commencez par vérifier si la variable membre correspondante est `Nothing`. Dans ce cas, une méthode d’initialisation est appelée, ce qui crée une instance de la variable de membre et attribue les principales propriétés liées à la commande.

## <a name="step-4-exposing-command-level-settings"></a>Étape 4 : Exposer des paramètres au niveau de la commande

Dans l’idéal, les informations au niveau de la commande doivent rester encapsulées dans la couche d’accès aux données. Ces informations doivent être nécessaires dans les autres couches de l’architecture, toutefois, il peut être exposé via une classe partielle, tout comme avec les paramètres au niveau de la connexion.

Étant donné que le TableAdapter a uniquement un seul `Connection` propriété, le code qui permet d’exposer les paramètres au niveau de la connexion est assez simple. Éléments sont un peu plus complexes lorsque vous modifiez les paramètres au niveau de la commande, car le TableAdapter peut avoir plusieurs objets command - une `InsertCommand`, `UpdateCommand`, et `DeleteCommand`, ainsi que d’un nombre variable d’objets de commande dans le `CommandCollection` propriété. Lors de la mise à jour des paramètres au niveau de la commande, ces paramètres seront doivent être propagées à tous les objets de commande.

Par exemple, imaginez que certaines requêtes sont dans le TableAdapter qui a pris un temps extraordinaire à s’exécuter. Lorsque vous utilisez le TableAdapter pour exécuter l’une de ces requêtes, nous voulons augmenter l’objet de commande s [ `CommandTimeout` propriété](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.commandtimeout.aspx). Cette propriété spécifie le nombre de secondes d’attente de la commande à exécuter et la valeur par défaut est 30.

Pour permettre la `CommandTimeout` propriété être ajustée par la couche BLL, ajoutez le code suivant `Public` méthode à la `ProductsDataTable` à l’aide du fichier de classe partielle créée à l’étape 2 (`ProductsTableAdapter.ConnectionAndCommandSettings.vb`) :


[!code-vb[Main](configuring-the-data-access-layer-s-connection-and-command-level-settings-vb/samples/sample4.vb)]

Cette méthode peut être appelée à partir de la couche BLL ou une couche de présentation pour définir le délai d’attente de commande pour tous les problèmes de commandes par cette instance du TableAdapter.

> [!NOTE]
> Le `Adapter` et `CommandCollection` propriétés sont marquées comme `Private`, ce qui signifie que leur est accessible uniquement à partir du code dans le TableAdapter. Contrairement à la `Connection` propriété, ces modificateurs d’accès ne sont pas configurables. Par conséquent, si vous avez besoin d’exposer les propriétés au niveau de la commande à d’autres couches de l’architecture, vous devez utiliser l’approche de la classe partielle mentionnée ci-dessus pour fournir un `Public` méthode ou propriété qui lit ou écrit dans le `Private` objets de commande.


## <a name="summary"></a>Récapitulatif

Les TableAdapters dans un DataSet typé servent pour encapsuler les détails de l’accès aux données et la complexité. À l’aide des TableAdapters, ne pas avoir à vous soucier de l’écriture de code ADO.NET pour se connecter à la base de données, exécutez une commande ou remplir les résultats dans un DataTable. Tout est traité automatiquement pour nous.

Toutefois, il peut arriver lorsque nous aurons besoin pour personnaliser les caractéristiques d’ADO.NET bas niveau, telles que la modification de la chaîne de connexion ou les valeurs de délai d’attente de connexion ou de commande par défaut. Le TableAdapter a généré automatiquement `Connection`, `Adapter`, et `CommandCollection` propriétés, mais ces derniers sont `Friend` ou `Private`, par défaut. Ces informations internes peuvent être exposées en étendant le TableAdapter à l’aide des classes partielles pour inclure `Public` méthodes ou propriétés. Vous pouvez également le TableAdapter s `Connection` modificateur d’accès de propriété peut être configuré via le TableAdapter s `ConnectionModifier` propriété.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Burnadette Leigh, S ren Jacob Lauritsen, Teresa Murphy et Hilton Geisenow. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](working-with-computed-columns-vb.md)
[Suivant](protecting-connection-strings-and-other-configuration-information-vb.md)
