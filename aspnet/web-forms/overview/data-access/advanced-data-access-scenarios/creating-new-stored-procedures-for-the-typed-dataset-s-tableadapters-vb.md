---
uid: web-forms/overview/data-access/advanced-data-access-scenarios/creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb
title: "Création de procédures stockées pour TableAdapters le groupe de données typé (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans les didacticiels précédentes, nous avons dans notre code d’instructions SQL créées et passé les instructions pour la base de données doit être exécuté. Une autre approche consiste à utiliser s..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/18/2007
ms.topic: article
ms.assetid: a5a4a9ba-d18d-489a-a6b0-a3c26d6b0274
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/advanced-data-access-scenarios/creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb
msc.type: authoredcontent
ms.openlocfilehash: b2262df1a56ffa88a22d9dc8000bd0c300fea72e
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="creating-new-stored-procedures-for-the-typed-datasets-tableadapters-vb"></a>Création de procédures stockées pour TableAdapters le groupe de données typé (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_67_VB.zip) ou [télécharger le PDF](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/datatutorial67vb1.pdf)

> Dans les didacticiels précédentes, nous avons dans notre code d’instructions SQL créées et passé les instructions pour la base de données doit être exécuté. Une autre approche consiste à utiliser des procédures stockées, où les instructions SQL sont prédéfinies dans la base de données. Dans ce didacticiel, nous apprendre pour que l’Assistant TableAdapter à générer de nouvelles procédures stockées pour nous.


## <a name="introduction"></a>Introduction

La couche DAL (Data Access) pour ces didacticiels utilise typés. Comme indiqué dans le [création d’une couche d’accès aux données](../introduction/creating-a-data-access-layer-vb.md) didacticiel, les DataSets typés sont constituées des TableAdapters et les tables de données fortement typées. Les tables de données représentent les entités logiques dans le système lors de l’interface de TableAdapters avec la base de données sous-jacente pour effectuer le travail d’accès aux données. Cela inclut les remplir les tables de données avec des données, l’exécution de requêtes qui retournent des données scalaires, insertion, mise à jour et suppression d’enregistrements dans la base de données.

Les commandes SQL exécutées par les TableAdapters peuvent être soit des instructions SQL ad hoc, tel que `SELECT columnList FROM TableName`, ou des procédures stockées. Les TableAdapters dans notre architecture utiliser des instructions SQL ad hoc. Nombreux développeurs et administrateurs de base de données, préfèrent Toutefois, les procédures stockées sur les instructions SQL ad hoc pour des raisons de sécurité, de facilité de maintenance et de mise à jour. D’autres préfèrent ardently les instructions SQL ad hoc pour leur flexibilité. Dans mon propre travail I privilégient les procédures stockées sur les instructions SQL ad hoc, mais a choisi d’utiliser des instructions SQL ad hoc pour simplifier les didacticiels antérieures.

Lorsque la définition d’un TableAdapter ou d’ajouter de nouvelles méthodes, l’Assistant TableAdapter s rend comme facile de créer des procédures stockées ou utiliser des procédures stockées existantes comme il le fait pour utiliser des instructions SQL ad hoc. Dans ce didacticiel, nous allons examiner comment l’Assistant TableAdapter s création automatique des procédures stockées. Dans l’étape suivante du didacticiel, nous allons examiner comment configurer les méthodes de s TableAdapter pour utiliser des procédures stockées existantes ou créées manuellement.

> [!NOTE]
> Consultez le billet de blog de Rob Howard [ne pas utiliser encore les procédures stockées ?](http://grokable.com/2003/11/dont-use-stored-procedures-yet-must-be-suffering-from-nihs-not-invented-here-syndrome/) et [Frans Bouma](https://weblogs.asp.net/fbouma/) une entrée de blog s [de procédures stockées sont défectueux, M Kay ?](https://weblogs.asp.net/fbouma/archive/2003/11/18/38178.aspx) pour un débat engageante sur les avantages et inconvénients de procédures stockées et les SQL ad hoc.


## <a name="stored-procedure-basics"></a>Présentation des procédures stockées

Les fonctions sont une construction commune à tous les langages de programmation. Une fonction est une collection d’instructions qui sont exécutées lorsque la fonction est appelée. Les fonctions peuvent accepter des paramètres d’entrée et peuvent éventuellement retourner une valeur. *[Procédures stockées](http://en.wikipedia.org/wiki/Stored_procedure)*  sont des constructions de base de données qui partagent de nombreuses similitudes avec les fonctions dans les langages de programmation. Une procédure stockée est constituée d’un ensemble d’instructions T-SQL qui sont exécutées lorsque la procédure stockée est appelée. Une procédure stockée peut accepter de zéro pour nombreux paramètres d’entrée et peut retourner des valeurs scalaires, des paramètres de sortie, ou plus souvent, de jeux de résultats de `SELECT` des requêtes.

> [!NOTE]
> Les procédures stockées sont souvent appelées sprocs ou SPs.


Les procédures stockées sont créées à l’aide de la [ `CREATE PROCEDURE` ](https://msdn.microsoft.com/library/aa258259(SQL.80).aspx) instruction T-SQL. Par exemple, le script T-SQL suivant crée une procédure stockée nommée `GetProductsByCategoryID` qui prend un paramètre unique nommé `@CategoryID` et retourne le `ProductID`, `ProductName`, `UnitPrice`, et `Discontinued` les champs de ces colonnes dans le `Products` table qui a une correspondance `CategoryID` valeur :


[!code-sql[Main](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample1.sql)]

Une fois cette procédure stockée a été créée, il peut être appelé à l’aide de la syntaxe suivante :


[!code-sql[Main](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample2.sql)]

> [!NOTE]
> Dans l’étape suivante du didacticiel, nous allons examiner de création de procédures stockées via l’IDE de Visual Studio. Pour ce didacticiel, toutefois, nous allons pour laisser l’Assistant TableAdapter générer automatiquement les procédures stockées pour nous.


En plus de retourner simplement les données, procédures stockées sont souvent utilisées pour exécuter plusieurs commandes de base de données dans l’étendue d’une transaction unique. Une procédure stockée nommée `DeleteCategory`, par exemple, peut prendre une `@CategoryID` paramètre et effectuer deux `DELETE` instructions : tout d’abord, un pour supprimer les produits connexes et une seconde lors de la suppression de la catégorie spécifiée. Plusieurs instructions dans une procédure stockée sont *pas* automatiquement encapsulée dans une transaction. Les commandes de T-SQL supplémentaires doivent être émis pour vous assurer de la procédure stockée s que plusieurs commandes sont traitées comme une opération atomique. Nous verrons comment exposer des commandes une procédure stockée dans l’étendue d’une transaction dans le didacticiel suivante.

Lorsque vous utilisez des procédures stockées au sein d’une architecture, les méthodes de s couche d’accès aux données appellent une procédure stockée au lieu d’émettre une instruction de SQL ad hoc. Cela centralise l’emplacement des instructions SQL exécutées (sur la base de données) au lieu d’avoir défini au sein de l’architecture d’application s. Cette centralisation rend plus facile à trouver, analyser et paramétrer les requêtes sans doute et fournit beaucoup plus claire qu’où et comment la base de données est utilisé.

Pour plus d’informations sur les notions de base de procédure stockée, consultez les ressources dans la section obtenir des informations supplémentaires à la fin de ce didacticiel.

## <a name="step-1-creating-the-advanced-data-access-layer-scenarios-web-pages"></a>Étape 1 : Création des Pages Web du scénarios de couche d’accès d’avancée des données

Avant de commencer à notre discussion sur la création de la couche DAL à l’aide de procédures stockées, permettent de s tout d’abord prendre quelques instants pour créer les pages ASP.NET dans notre projet de site Web est nécessaire pour cette fonctionnalité et les didacticiels plusieurs suivants. Commencez par ajouter un nouveau dossier nommé `AdvancedDAL`. Ensuite, ajoutez les pages ASP.NET suivantes à ce dossier, en veillant à associer chaque page avec le `Site.master` page maître :

- `Default.aspx`
- `NewSprocs.aspx`
- `ExistingSprocs.aspx`
- `JOINs.aspx`
- `AddingColumns.aspx`
- `ComputedColumns.aspx`
- `EncryptingConfigSections.aspx`
- `ManagedFunctionsAndSprocs.aspx`


![Ajouter les Pages ASP.NET pour les didacticiels de scénarios de couche données avancés accès](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image1.png)

**Figure 1**: ajouter les Pages ASP.NET pour les didacticiels de scénarios de couche données avancés accès


Comme dans les autres dossiers, `Default.aspx` dans le `AdvancedDAL` dossier répertorie les didacticiels dans sa section. N’oubliez pas que le `SectionLevelTutorialListing.ascx` contrôle utilisateur fournit cette fonctionnalité. Par conséquent, ajoutez ce contrôle utilisateur pour `Default.aspx` en le faisant glisser à partir de l’Explorateur de solutions sur la page s mode Création.


[![Ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image3.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image2.png)

**Figure 2**: ajouter la `SectionLevelTutorialListing.ascx` contrôle utilisateur à `Default.aspx` ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image4.png))


Enfin, ajoutez ces pages en tant qu’entrées pour les `Web.sitemap` fichier. En particulier, ajoutez le balisage suivant après la travail avec des données par lots `<siteMapNode>`:


[!code-xml[Main](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample3.xml)]

Après la mise à jour `Web.sitemap`, prenez un moment pour afficher le site Web des didacticiels via un navigateur. Le menu de gauche inclut désormais les éléments pour les didacticiels de scénarios avancés DAL.


![Le plan de Site inclut maintenant des entrées pour les didacticiels de scénarios avancés DAL](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image5.png)

**Figure 3**: le plan de Site inclut maintenant des entrées pour les didacticiels de scénarios avancés DAL


## <a name="step-2-configuring-a-tableadapter-to-create-new-stored-procedures"></a>Étape 2 : Configuration d’un TableAdapter à créer des procédures stockées

Pour illustrer la création d’une couche d’accès aux données qui utilise des procédures stockées au lieu d’instructions SQL ad hoc, s permettent de créer un DataSet typé dans les `~/App_Code/DAL` dossier nommé `NorthwindWithSprocs.xsd`. Étant donné que nous avons présenté ce processus en détail dans les didacticiels précédents, nous continuerons rapidement à travers les étapes ici. Si vous êtes bloqué ou que vous avez besoin d’obtenir des instructions détaillées dans la création et configuration d’un DataSet typé, faire référence à la [création d’une couche d’accès aux données](../introduction/creating-a-data-access-layer-vb.md) didacticiel.

Ajouter un nouveau DataSet au projet en cliquant sur le `DAL` dossier, en choisissant Ajouter un nouvel élément et en sélectionnant le modèle de jeu de données, comme indiqué dans la Figure 4.


[![Ajouter un nouveau DataSet typé au projet nommé NorthwindWithSprocs.xsd](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image7.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image6.png)

**Figure 4**: ajouter un nouveau DataSet typé pour le projet nommé `NorthwindWithSprocs.xsd` ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image8.png))


Cela créera le nouveau DataSet typé, ouvrez son concepteur, créez un nouveau TableAdapter et lancer l’Assistant Configuration de TableAdapter. Assistant Configuration de TableAdapter s première étape nous demande de sélectionner la base de données à utiliser. La chaîne de connexion à la base de données Northwind doit être répertoriée dans la liste déroulante. Sélectionnez cette option et cliquez sur Suivant.

À partir de cet écran suivant, nous pouvons choisir comment le TableAdapter doit-il accéder à la base de données. Dans les didacticiels précédents, nous avons sélectionné la première option, utiliser des instructions SQL. Pour ce didacticiel, sélectionnez la deuxième option, créer des procédures stockées et cliquez sur Suivant.


[![Indiquer le TableAdpater pour créer des procédures stockées](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image10.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image9.png)

**Figure 5**: indiquer le TableAdpater pour créer de nouvelles procédures stockées ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image11.png))


Comme à l’aide des instructions SQL ad hoc, à l’étape suivante est une demande de fournir la `SELECT` instruction de la requête principale de TableAdapter s. Mais au lieu d’utiliser le `SELECT` instruction entrée ici pour effectuer une requête ad hoc directement, l’Assistant TableAdapter s créera une procédure stockée qui contient ce `SELECT` requête.

Utilisez ce qui suit `SELECT` requête pour ce TableAdapter :


[!code-sql[Main](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample4.sql)]


[![Entrez la requête SELECT](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image13.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image12.png)

**Figure 6**: entrez le `SELECT` requête ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image14.png))


> [!NOTE]
> La requête ci-dessus diffère légèrement de la requête principale de la `ProductsTableAdapter` dans le `Northwind` DataSet typé. N’oubliez pas que le `ProductsTableAdapter` dans le `Northwind` Typed DataSet inclut deux sous-requêtes en corrélation pour remettre le nom de la catégorie et le nom de société pour chaque catégorie de produit s et un fournisseur. Dans la [mise à jour le TableAdapter à l’utilisation de jointures](updating-the-tableadapter-to-use-joins-vb.md) didacticiel, nous allons nous intéresser à l’ajout des données liées à ce TableAdapter.


Prenez un moment pour cliquer sur le bouton Options avancées. À partir d’ici, nous pouvons spécifier si l’Assistant doit également générer des instructions delete, update et insert pour le TableAdapter, s’il faut utiliser l’accès concurrentiel optimiste et indique si la table de données doit être actualisée après les insertions et mises à jour. L’option d’instructions générer Insert, Update et Delete est activée par défaut. Laissez activée. Pour ce didacticiel, laissez les options de l’accès concurrentiel optimiste utiliser désactivée.

Lorsque having les procédures stockées créées automatiquement par l’Assistant TableAdapter, il apparaît que l’actualisation de l’option de table de données est ignorée. Indépendamment de si cette case à cocher est activée, l’insertion résultante et mise à jour des procédures stockées récupérer l’enregistrement de juste insérée ou mise à jour juste, comme nous allons le voir à l’étape 3.


![Laissez l’Option activée, les instructions générer Insert, Update et Delete](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image15.png)

**Figure 7**: laissez l’Option activée, les instructions générer Insert, Update et Delete


> [!NOTE]
> Si l’option d’utilisation d’accès concurrentiel optimiste est activée, l’Assistant ajoutera des conditions supplémentaires pour le `WHERE` clause qui empêchent la mise à jour si des modifications ont été dans d’autres champs de données. Faire référence à la [implémentation de l’accès concurrentiel optimiste](../editing-inserting-and-deleting-data/implementing-optimistic-concurrency-vb.md) didacticiel pour plus d’informations sur l’utilisation de la fonctionnalité de contrôle TableAdapter s intégré d’accès concurrentiel optimiste.


Après avoir entré la `SELECT` interroger et confirmé que l’option d’instructions générer Insert, Update et Delete est activée, cliquez sur Suivant. Cet écran suivant, illustré dans la Figure 8, vous invite à entrer pour les noms des procédures stockées, que l’Assistant va créer pour la sélection, insertion, mise à jour et suppression de données. Modifier ces noms des procédures de stockage `Products_Select`, `Products_Insert`, `Products_Update`, et `Products_Delete`.


[![Renommer les procédures stockées](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image17.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image16.png)

**Figure 8**: renommer des procédures stockées ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image18.png))


Pour afficher le code T-SQL, l’Assistant TableAdapter utilisera pour créer les quatre procédures stockées, cliquez sur le bouton Aperçu le Script SQL. À partir de la boîte de dialogue de Script SQL de version préliminaire, vous pouvez enregistrer le script dans un fichier ou le copier dans le Presse-papiers.


![Afficher un aperçu du Script SQL utilisé pour générer les procédures stockées](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image19.png)

**Figure 9**: aperçu du Script SQL utilisé pour générer les procédures stockées


Après la désignation des procédures stockées, cliquez sur Suivant pour les méthodes correspondantes nom s TableAdapter. Comme cas à l’aide des instructions SQL ad hoc, nous pouvons créer des méthodes de remplir un DataTable existant ou retournent un nouveau. Nous pouvons également spécifier si le TableAdapter doit inclure le modèle Direct à la base de données pour insérer, mettre à jour et suppression d’enregistrements. Laissez toutes les cases à trois cocher vérifiées, mais renommer le retour d’une méthode de DataTable à `GetProducts` (comme indiqué dans la Figure 10).


[![Nommez les méthodes de remplissage et GetProducts](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image21.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image20.png)

**La figure 10**: nommez les méthodes `Fill` et `GetProducts` ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image22.png))


Cliquez sur Suivant pour consulter un résumé des étapes de que l’Assistant va effectuer. Terminez l’Assistant en cliquant sur le bouton Terminer. Une fois l’Assistant terminé, vous serez renvoyé au jeu de données s concepteur, qui doit inclure désormais le `ProductsDataTable`.


[![Le Concepteur de s DataSet montre la ProductsDataTable nouvellement ajoutée](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image24.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image23.png)

**Figure 11**: le DataSet s Concepteur montre récemment ajouté `ProductsDataTable` ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image25.png))


## <a name="step-3-examining-the-newly-created-stored-procedures"></a>Étape 3 : Examiner les procédures stockées qui vient d’être créés.

L’Assistant de TableAdapter utilisé automatiquement à l’étape 2 a créé les procédures stockées pour la sélection, insertion, mise à jour et suppression de données. Ces procédures stockées peuvent être visualisées ou modifiées via Visual Studio en accédant à l’Explorateur de serveurs et d’exploration vers le bas dans le dossier des procédures stockées de base de données. Comme le montre la Figure 12, la base de données Northwind contient quatre nouvelles procédures stockées : `Products_Delete`, `Products_Insert`, `Products_Select`, et `Products_Update`.


![Vous trouverez les quatre procédures stockées créées à l’étape 2 dans le dossier de procédures stockées de base de données s](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image26.png)

**Figure 12**: quatre procédures stockées créées à l’étape 2 peuvent être trouvées dans le dossier de procédures stockées de base de données s


> [!NOTE]
> Si vous ne voyez pas l’Explorateur de serveurs, ouvrez le menu Affichage et choisissez l’option de l’Explorateur de serveurs. Si vous ne voyez pas les procédures stockées de produit ajoutés à l’étape 2, essayez en cliquant sur le dossier Stored Procedures actualise.


Pour afficher ou modifier une procédure stockée, double-cliquez sur son nom dans l’Explorateur de serveurs ou, vous pouvez également, avec le bouton droit sur la procédure stockée et choisissez Ouvrir. Figure 13 illustre le `Products_Delete` procédure stockée, l’ouverture.


[![Les procédures stockées peuvent être ouvert et modifiés dans Visual Studio](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image28.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image27.png)

**Figure 13**: stockées procédures peuvent être ouverts et modifiés à partir de Visual Studio ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image29.png))


Le contenu des deux le `Products_Delete` et `Products_Select` les procédures stockées sont assez simples. Le `Products_Insert` et `Products_Update` des procédures stockées, justifient quant à eux, un examen approfondi, car ils sont tous deux effectuent un `SELECT` instruction après leur `INSERT` et `UPDATE` instructions. Par exemple, le code SQL suivant constitue le `Products_Insert` procédure stockée :


[!code-sql[Main](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample5.sql)]

La procédure stockée accepte en tant que paramètres d’entrée le `Products` les colonnes qui ont été retournées par la `SELECT` requête spécifiée dans l’Assistant TableAdapter s et ces valeurs sont utilisées dans un `INSERT` instruction. Suivant le `INSERT` instruction, un `SELECT` requête est utilisée pour retourner le `Products` les valeurs de colonne (y compris le `ProductID`) de l’enregistrement récemment ajouté. Cette fonction d’actualisation est utile lorsque vous ajoutez un nouvel enregistrement à l’aide du modèle de mise à jour par lots telle qu’elle automatiquement des mises à jour récemment ajouté `ProductRow` instances `ProductID` propriétés avec les valeurs incrémentées automatiquement affectés à la base de données.

Le code suivant illustre cette fonctionnalité. Il contient un `ProductsTableAdapter` et `ProductsDataTable` créé pour le `NorthwindWithSprocs` DataSet typé. Un nouveau produit est ajouté à la base de données en créant un `ProductsRow` instance, qui fournit ses valeurs et en appelant le TableAdapter s `Update` méthode, en passant le `ProductsDataTable`. En interne, le TableAdapter s `Update` méthode énumère les `ProductsRow` instances dans le DataTable dans le passé (dans cet exemple montre comment il n'est qu’un seul - celui que nous venons d’ajouter) et effectue approprié insérer, mettre à jour ou supprimer des commandes. Dans ce cas, le `Products_Insert` procédure stockée est exécutée, ce qui ajoute un nouvel enregistrement à la `Products` de table et retourne les détails de l’enregistrement qui vient d’être ajouté. Le `ProductsRow` instance s `ProductID` valeur est mise à jour. Après le `Update` méthode est terminée, nous pouvons accéder à l’enregistrement récemment ajouté s `ProductID` valeur via la `ProductsRow` s `ProductID` propriété.


[!code-vb[Main](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample6.vb)]

Le `Products_Update` procédure stockée même inclut un `SELECT` instruction après son `UPDATE` instruction.


[!code-sql[Main](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample7.sql)]

Notez que cette procédure stockée comprend deux paramètres d’entrée pour `ProductID`: `@Original_ProductID` et `@ProductID`. Cette fonctionnalité permet les scénarios où la clé primaire peut être modifiée. Par exemple, dans une base de données employés, chaque enregistrement d’employé peut utiliser le numéro de sécurité sociale s employé en tant que leur clé primaire. Pour modifier un numéro de sécurité sociale de s employé existant, le nouveau numéro de sécurité sociale et d’origine doivent être fournis. Pour le `Products` table, cette fonctionnalité n’est pas nécessaire car le `ProductID` colonne est un `IDENTITY` colonne et ne doivent jamais être modifiés. En fait, le `UPDATE` instruction dans le `Products_Update` procédure stockée n’inclure la `ProductID` colonne dans sa liste de colonnes. Par conséquent, tandis que `@Original_ProductID` est utilisé dans le `UPDATE` instruction s `WHERE` clause, il est superflu de la `Products` table et peut être remplacé par le `@ProductID` paramètre. Lorsque vous modifiez un s paramètres de procédure stockée, il est important que l’ou les méthodes TableAdapter qui utilisent cette procédure stockée sont également mis à jour.

## <a name="step-4-modifying-a-stored-procedure-s-parameters-and-updating-the-tableadapter"></a>Étape 4 : Modification d’une procédure stockée s paramètres et la mise à jour du TableAdapter

Étant donné que la `@Original_ProductID` paramètre est superflu, s permettent de supprimer de la `Products_Update` procédure stockée complètement. Ouvrir le `Products_Update` procédure stockée, supprimer le `@Original_ProductID` paramètre, puis, dans le `WHERE` clause de la `UPDATE` instruction, modifiez le nom de paramètre utilisé à partir de `@Original_ProductID` à `@ProductID`. Après avoir apporté ces modifications, le code T-SQL dans la procédure stockée doit se présenter comme suit :


[!code-sql[Main](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample8.sql)]

Pour enregistrer ces modifications dans la base de données, cliquez sur l’icône Enregistrer dans la barre d’outils ou appuyez sur Ctrl + S. À ce stade, le `Products_Update` n’attend pas de procédure stockée une `@Original_ProductID` paramètre d’entrée, mais le TableAdapter est configuré pour passer un paramètre de ce type. Vous pouvez voir les paramètres TableAdapter enverra à la `Products_Update` procédure stockée, en sélectionnant le TableAdapter dans le Concepteur de DataSet, accédant à la fenêtre Propriétés, cliquez sur le bouton de sélection dans le `UpdateCommand` s `Parameters` collection. Cela affiche la boîte de dialogue Éditeur de collections Parameters indiquée dans la Figure 14.


![Les listes de l’éditeur de collections de paramètres les paramètres utilisés passé à la Products_Update procédure stockée](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image30.png)

**La figure 14**: les listes de l’éditeur de collections de paramètres les paramètres utilisés passé à la `Products_Update` procédure stockée


Vous pouvez supprimer ce paramètre à partir d’ici en sélectionnant simplement la `@Original_ProductID` paramètre dans la liste des membres et en cliquant sur le bouton Supprimer.

Ou bien, vous pouvez actualiser les paramètres utilisés pour toutes les méthodes en cliquant sur le TableAdapter dans le concepteur et en choisissant de configurer. Cela affiche l’Assistant Configuration de TableAdapter, qui répertorie les procédures stockées utilisées pour la sélection, insertion, mise à jour, et la suppression, ainsi que les paramètres des procédures stockées s’attendre à recevoir. Si vous cliquez sur la liste déroulante de mise à jour, vous pouvez voir les `Products_Update` des procédures stockées attendu des paramètres d’entrée, qui inclut maintenant n’est plus `@Original_ProductID` (voir Figure 15). Cliquez simplement sur Terminer pour mettre à jour automatiquement la collection de paramètres utilisée par le TableAdapter.


[![Vous pouvez également utiliser l’Assistant Configuration de TableAdapter s pour actualiser ses Collections de paramètres de méthodes](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image32.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image31.png)

**Figure 15**: vous pouvez également utiliser le TableAdapter s Assistant de Configuration pour les Collections de paramètres méthodes actualiser son ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image33.png))


## <a name="step-5-adding-additional-tableadapter-methods"></a>Étape 5 : Ajout de méthodes TableAdapter supplémentaires

En tant qu’étape 2 illustre, lors de la création d’un nouveau TableAdapter, il est facile des procédures stockées correspondantes générés automatiquement. Il en est de même lorsque vous ajoutez des méthodes supplémentaires à un TableAdapter. Pour illustrer cela, permettent de s ajouter un `GetProductByProductID(productID)` méthode à la `ProductsTableAdapter` créé à l’étape 2. Cette méthode prend comme entrée un `ProductID` valeur et retourner des détails sur le produit spécifié.

Démarrez en cliquant sur le TableAdapter et en choisissant Ajouter une requête dans le menu contextuel.


![Ajouter une nouvelle requête au TableAdapter](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image34.png)

**Figure 16**: ajouter une nouvelle requête au TableAdapter


Démarre l’Assistant Configuration de requête TableAdapter, qui demande tout d’abord comment le TableAdapter doit-il accéder à la base de données. Pour une nouvelle procédure stockée créée, choisissez de créer une nouvelle option de procédure stockée, puis cliquez sur Suivant.


[![Choisissez de créer une nouvelle Option de procédure stockée](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image36.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image35.png)

**Figure 17**: choisissez de créer une nouvelle Option de procédure stockée ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image37.png))


L’écran suivant demande nous d’identifier le type de requête à exécuter, s’il sera renvoyer un ensemble de lignes ou une valeur scalaire unique, ou effectuer une `UPDATE`, `INSERT`, ou `DELETE` instruction. Étant donné que la `GetProductByProductID(productID)` méthode sera retourner une ligne, laissez l’instruction SELECT qui retourne une option de la ligne sélectionnée et d’accès suivant.


[![Choisissez l’instruction SELECT qui retourne l’Option de ligne](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image39.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image38.png)

**La figure 18**: choisissez l’instruction SELECT qui retourne l’Option de ligne ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image40.png))


L’écran suivant affiche la requête principale s du TableAdapter, qui répertorie les simplement le nom de la procédure stockée (`dbo.Products_Select`). Remplacez le nom de la procédure stockée par le code suivant `SELECT` instruction, qui retourne tous les champs de produit pour un produit donné :


[!code-sql[Main](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample9.sql)]


[![Remplacez le nom de la procédure stockée par une requête SELECT](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image42.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image41.png)

**Figure 19**: Remplacez le nom de la procédure stockée avec un `SELECT` requête ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image43.png))


L’écran suivant vous demande de nom de la procédure stockée qui sera créée. Entrez le nom `Products_SelectByProductID` et cliquez sur Suivant.


[![Nom de la nouvelle Products_SelectByProductID de procédure stockée](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image45.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image44.png)

**Figure 20**: nom de la nouvelle procédure stockée `Products_SelectByProductID` ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image46.png))


L’étape finale de l’Assistant permet de modifier la méthode noms générés ainsi que pour indiquent s’il faut utiliser le remplissage de modèle d’un DataTable, retourner un DataTable de modèle, ou les deux. Pour cette méthode, laissez les deux options activées, mais renommer les méthodes à `FillByProductID` et `GetProductByProductID`. Cliquez sur Suivant pour afficher un résumé des étapes de l’Assistant effectuer et puis cliquez sur Terminer pour terminer l’Assistant.


[![Renommez les méthodes du TableAdapter que s FillByProductID et GetProductByProductID](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image48.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image47.png)

**Figure 21**: Renommez les méthodes de s TableAdapter à `FillByProductID` et `GetProductByProductID` ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image49.png))


Après la fin de l’Assistant, le TableAdapter dispose d’une nouvelle méthode, `GetProductByProductID(productID)` qui, lorsqu’elle est appelée, exécute le `Products_SelectByProductID` procédure stockée qui vient d’être créé. Prenez un moment pour afficher cette nouvelle procédure stockée à partir de l’Explorateur de serveurs en extrayant le dossier Stored Procedures et en ouvrant `Products_SelectByProductID` (si vous ne le voyez pas, avec le bouton droit sur le dossier Stored Procedures, cliquez sur Actualiser).

Notez que la `SelectByProductID` stockées procédure prend `@ProductID` comme paramètre d’entrée et exécute la `SELECT` instruction nous entrés dans l’Assistant.


[!code-sql[Main](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample10.sql)]

## <a name="step-6-creating-a-business-logic-layer-class"></a>Étape 6 : Création d’une classe de couche de logique métier

Dans l’ensemble de la série de didacticiels, nous avons cherché à mettre à jour d’une architecture multiniveau où la couche de présentation effectuée tous ses appels à la couche BLL (Business Logic). Afin de respecter cette décision de conception, nous devons créer une classe de la couche BLL pour le nouveau DataSet typé avant que nous pouvons accéder à des données de produit à partir de la couche de présentation.

Créer un nouveau fichier de classe nommé `ProductsBLLWithSprocs.vb` dans le `~/App_Code/BLL` dossier et lui ajouter le code suivant :


[!code-vb[Main](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample11.vb)]

Imite de cette classe le `ProductsBLL` classe la sémantique de didacticiels antérieures, mais utilise le `ProductsTableAdapter` et `ProductsDataTable` des objets de la `NorthwindWithSprocs` jeu de données. Par exemple, au lieu d’avoir un `Imports NorthwindTableAdapters` instruction au début du fichier de classe en tant que `ProductsBLL` est le cas, le `ProductsBLLWithSprocs` classe utilise `Imports NorthwindWithSprocsTableAdapters`. De même, la `ProductsDataTable` et `ProductsRow` objets utilisés dans cette classe sont précédées du `NorthwindWithSprocs` espace de noms. Le `ProductsBLLWithSprocs` classe fournit des méthodes d’accès aux données de deux `GetProducts` et `GetProductByProductID`, et les méthodes pour ajouter, mettre à jour et supprimer une instance de produit unique.

## <a name="step-7-working-with-thenorthwindwithsprocsdataset-from-the-presentation-layer"></a>Étape 7 : Utilisation de la`NorthwindWithSprocs`DataSet à partir de la couche de présentation

À ce stade, nous avons créé une DAL qui utilise des procédures stockées pour accéder et modifier la base de données sous-jacente. Nous avons également créé une couche BLL rudimentaire avec des méthodes pour récupérer tous les produits ou un produit particulier, ainsi que des méthodes pour l’ajout, la mise à jour et suppression des produits. Pour arrondir ce didacticiel, s permettent de créer une page ASP.NET qui utilise la couche BLL s `ProductsBLLWithSprocs` classe pour l’affichage, la mise à jour et suppression d’enregistrements.

Ouvrir le `NewSprocs.aspx` page dans le `AdvancedDAL` faites glisser un contrôle GridView à partir de la boîte à outils vers le concepteur, nommez-la `Products`. Le contrôle GridView balise s Choisissez Lier à un nouveau ObjectDataSource nommé `ProductsDataSource`. Configurer l’ObjectDataSource à utiliser le `ProductsBLLWithSprocs` de classe, comme indiqué dans la Figure 22.


[![Configurer pour utiliser la classe ProductsBLLWithSprocs ObjectDataSource](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image51.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image50.png)

**La figure 22**: configurer ObjectDataSource à utiliser le `ProductsBLLWithSprocs` classe ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image52.png))


La liste déroulante dans l’onglet sélection a deux options, `GetProducts` et `GetProductByProductID`. Étant donné que nous souhaitons afficher tous les produits dans le GridView, choisissez le `GetProducts` (méthode). Les listes déroulantes dans les onglets UPDATE, INSERT et DELETE ont uniquement une méthode. Assurez-vous que chacune de ces listes déroulantes dispose de sa méthode approprié sélectionné, puis sur Terminer.

Une fois l’Assistant ObjectDataSource terminée, Visual Studio ajoute BoundFields et un CheckBoxField au GridView pour les champs de données de produit. Activez le contrôle GridView s intégrés fonctionnalités d’édition et suppression en vérifiant les options Activer la suppression qui sont présentes dans la balise active et activer la modification.


[![La Page contient un GridView avec la modification et suppression de prise en charge](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image54.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image53.png)

**Figure 23**: la Page contient un GridView avec la modification et suppression de la prise en charge activée ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image55.png))


Que nous avons ve présentés dans les didacticiels précédents, à l’issue de l’Assistant s ObjectDataSource, jeux de Visual Studio le `OldValuesParameterFormatString` propriété original\_{0}. Il doit être restauré à sa valeur par défaut de {0} dans l’ordre pour les fonctionnalités de modification de données fonctionne correctement étant donné les paramètres attendus par les méthodes dans notre BLL. Par conséquent, veillez à définir le `OldValuesParameterFormatString` propriété à {0} ou supprimer complètement de la propriété à partir de la syntaxe déclarative.

Après la fin de l’Assistant Configurer la Source de données, modification et suppression de prise en charge dans le contrôle GridView et retourner le s ObjectDataSource sous tension `OldValuesParameterFormatString` propriété à sa valeur par défaut, votre balisage déclaratif s de page doit ressembler à ce qui suit :


[!code-aspx[Main](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample12.aspx)]

À ce stade nous aurions pu nettoyer le GridView en personnalisant l’interface de modification pour inclure la validation, ayant la `CategoryID` et `SupplierID` colonnes rendu en tant que la compréhension des listes et ainsi de suite. Nous avons également ajouter une confirmation côté client pour le bouton de suppression, et nous vous invitons à prendre le temps de mettre en œuvre ces améliorations. Étant donné que ces rubriques ont été traités dans les didacticiels précédents, nous n'est pas couvre les à nouveau ici.

Quelle que soit l’amélioration que le contrôle GridView ou non, tester les fonctionnalités principales de pages dans un navigateur. Comme le montre la Figure 24, la page répertorie les produits dans un GridView qui fournit la modification et suppression des fonctionnalités de ligne.


[![Les produits peuvent être affichées, modifiées et supprimées du contrôle GridView](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image57.png)](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image56.png)

**Figure 24**: les produits peuvent être affichés, modifié et supprimé du contrôle GridView ([cliquez pour afficher l’image en taille réelle](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image58.png))


## <a name="summary"></a>Récapitulatif

Les TableAdapters dans un DataSet typé peut accéder aux données à partir de la base de données à l’aide d’instructions SQL ad hoc ou via des procédures stockées. Utilisation des procédures stockées, soit les procédures stockées existantes peuvent être utilisés quand ou de l’Assistant TableAdapter peut être paramétrée pour créer de nouvelles procédures stockées basées sur un `SELECT` requête. Dans ce didacticiel, nous Explorer comment les procédures stockées créées automatiquement pour vous.

Bien que les procédures stockées générées automatiquement permet de gagner du temps, il existe certains cas où la procédure stockée créée par l’Assistant ne t s’alignent sur ce que nous a créé sur notre propre. Par exemple, le `Products_Update` procédure stockée, ce qui est attendu à la fois `@Original_ProductID` et `@ProductID` même si les paramètres d’entrée le `@Original_ProductID` paramètre était superflu.

Dans de nombreux scénarios, les procédures stockées peuvent avoir été déjà créés, ou nous pouvons choisir de les générer manuellement afin d’avoir un meilleur niveau de contrôle sur les commandes de s de procédure stockée. Dans les deux cas, nous ne souhaitez pas indiquer le TableAdapter à utiliser des procédures stockées existantes pour ses méthodes. Nous verrons comment accomplir cette tâche dans le didacticiel suivant.

Bonne programmation !

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Créer et maintenir les procédures stockées](https://msdn.microsoft.com/library/aa214299(SQL.80).aspx)
- [La récupération des données scalaires à partir d’une procédure stockée](http://aspnet.4guysfromrolla.com/articles/062905-1.aspx)
- [Présentation des procédures stockées de SQL Server](http://www.awprofessional.com/articles/article.asp?p=25288&amp;rl=1)
- [Procédures stockées : Une vue d’ensemble](http://www.sqlteam.com/item.asp?ItemID=563)
- [L’écriture d’une procédure stockée](http://www.4guysfromrolla.com/webtech/111499-1.shtml)

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Hilton Geisenow. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs.md)
[Suivant](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb.md)
