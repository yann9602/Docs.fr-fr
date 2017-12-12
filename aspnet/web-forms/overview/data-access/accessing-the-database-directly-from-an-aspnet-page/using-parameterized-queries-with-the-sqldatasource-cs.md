---
uid: web-forms/overview/data-access/accessing-the-database-directly-from-an-aspnet-page/using-parameterized-queries-with-the-sqldatasource-cs
title: "À l’aide de requêtes paramétrables avec le SqlDataSource (c#) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous continuer la présentation du contrôle SqlDataSource et apprendre à définir des requêtes paramétrables. Les paramètres peuvent être spécifiés à la fois decla..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2007
ms.topic: article
ms.assetid: 9128aaac-afe2-449f-84b2-bb1d035083c4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/accessing-the-database-directly-from-an-aspnet-page/using-parameterized-queries-with-the-sqldatasource-cs
msc.type: authoredcontent
ms.openlocfilehash: 7b32a664975254dcc1d015f2400df30d05346948
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-parameterized-queries-with-the-sqldatasource-c"></a>À l’aide de requêtes paramétrables avec le SqlDataSource (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_48_CS.exe) ou [télécharger le PDF](using-parameterized-queries-with-the-sqldatasource-cs/_static/datatutorial48cs1.pdf)

> Dans ce didacticiel, nous continuer la présentation du contrôle SqlDataSource et apprendre à définir des requêtes paramétrables. Les paramètres peuvent être spécifiés à la fois de façon déclarative et par programme et peuvent être extraites d’un nombre d’emplacements tels que la chaîne de requête, de Session état, autres contrôles et bien plus encore.


## <a name="introduction"></a>Introduction

Dans le didacticiel précédent, nous avons vu comment utiliser le contrôle SqlDataSource pour récupérer des données directement à partir d’une base de données. À l’aide de l’Assistant Configurer la Source de données, nous pouvons choisir la base de données, puis soit : choisir les colonnes à retourner à partir d’une table ou vue ; Entrez une instruction SQL personnalisée ; ou utilisez une procédure stockée. Si, en sélectionnant les colonnes à partir d’une table ou une vue ou en entrant une instruction SQL personnalisée, le SqlDataSource contrôle s `SelectCommand` est affectée à l’instruction SQL ad hoc obtenue `SELECT` instruction et il est-ce `SELECT` instruction est exécutée lorsque le SqlDataSource s `Select()` méthode est appelée (automatiquement ou par programme à partir d’un contrôle Web de données).

L’instruction SQL `SELECT` instructions utilisées dans les démonstrations didacticiel s précédent n’offraient pas `WHERE` clauses. Dans un `SELECT` instruction, le `WHERE` clause peut être utilisée pour limiter les résultats retournés. Par exemple, pour afficher les noms de produits d’évaluation des coûts plus de 50,00 $, nous pouvons utiliser la requête suivante :


[!code-sql[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample1.sql)]

En règle générale, les valeurs utilisées dans un `WHERE` clause sont déterminer par une source externe, comme une valeur de chaîne de requête, une variable de session ou des entrées utilisateur à partir d’un contrôle Web dans la page. Dans l’idéal, ces entrées sont spécifiées à l’aide de *paramètres*. Avec Microsoft SQL Server, les paramètres sont indiqués à l’aide de `@parameterName`, comme dans :


[!code-sql[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample2.sql)]

Prend en charge les requêtes paramétrables, à la fois pour le SqlDataSource `SELECT` instructions et `INSERT`, `UPDATE`, et `DELETE` instructions. En outre, les valeurs de paramètre peuvent être automatiquement extraites à partir de diverses sources de la chaîne de requête, l’état de session, les contrôles sur la page et ainsi de suite ou peuvent être affectées par programme. Dans ce didacticiel, nous verrons comment définir des requêtes paramétrables comment pour spécifier le paramètre des valeurs de façon déclarative et par programme.

> [!NOTE]
> Dans le didacticiel précédent, nous avons comparé ObjectDataSource qui a été notre outil de choix sur les 46 tout d’abord didacticiels avec le SqlDataSource, en indiquant leurs similitudes conceptuels. Ces similitudes s’étendent aussi à des paramètres. Les paramètres de s ObjectDataSource mappées aux paramètres d’entrée pour les méthodes dans la couche de logique métier. Avec le SqlDataSource, les paramètres sont définis directement dans la requête SQL. Les deux contrôles ont des collections de paramètres pour leurs `Select()`, `Insert()`, `Update()`, et `Delete()` des méthodes et les deux peuvent avoir ces valeurs de paramètre remplis à partir de sources prédéfinis (valeurs de chaîne de requête, les variables de session, etc. ) ou attribués par programme.


## <a name="creating-a-parameterized-query"></a>Création d'une requête paramétrée

L’Assistant Configurer la Source de données de SqlDataSource contrôle s offre trois outils différents pour la définition de la commande à exécuter pour récupérer les enregistrements de base de données :

- En choisissant les colonnes à partir d’une table ou une vue,
- En entrant une instruction SQL personnalisée, ou
- En choisissant une procédure stockée

Lors de la sélection des colonnes d’une table existante ou d’affichage, les paramètres de la `WHERE` clause doit être spécifiée via l’ajout de `WHERE` boîte de dialogue Clause. Lorsque vous créez une instruction SQL personnalisée, toutefois, vous pouvez entrer les paramètres directement dans le `WHERE` clause (à l’aide de `@parameterName` pour indiquer chaque paramètre). A [procédure stockée](http://www.awprofessional.com/articles/article.asp?p=25288&amp;rl=1) se compose d’une ou plusieurs instructions SQL, et ces instructions peuvent être paramétrées. Les paramètres utilisés dans les instructions SQL, toutefois, doivent être passés dans en tant que paramètres d’entrée à la procédure stockée.

Étant donné que la création d’une requête paramétrable dépend de la s SqlDataSource `SelectCommand` est spécifié, laissez s un coup de œil aux trois approches. Pour commencer, ouvrez le `ParameterizedQueries.aspx` page dans le `SqlDataSource` dossier, faites glisser un contrôle SqlDataSource à partir de la boîte à outils vers le concepteur et définir son `ID` à `Products25BucksAndUnderDataSource`. Ensuite, cliquez sur le lien configurer la Source de données à partir de la balise active de contrôle s. Sélectionnez la base de données à utiliser (`NORTHWINDConnectionString`) et cliquez sur Suivant.

## <a name="step-1-adding-awhereclause-when-picking-the-columns-from-a-table-or-view"></a>Étape 1 : Ajout d’un`WHERE`Clause lors de la sélection des colonnes à partir d’une Table ou une vue

Lorsque vous sélectionnez les données à retourner à partir de la base de données avec le contrôle SqlDataSource, l’Assistant Configurer la Source de données permet simplement de sélectionner les colonnes à retourner à partir d’une table existante ou afficher (voir Figure 1). Crée alors automatiquement SQL `SELECT` instruction, qui est envoyé à la base de données lorsque le s SqlDataSource `Select()` méthode est appelée. Comme nous l’avons fait dans le didacticiel précédent, sélectionnez la table Products dans la liste déroulante et vérifiez la `ProductID`, `ProductName`, et `UnitPrice` colonnes.


[![Choisir les colonnes à retourner à partir d’une Table ou une vue](using-parameterized-queries-with-the-sqldatasource-cs/_static/image1.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image1.png)

**Figure 1**: choisir les colonnes à retourner à partir d’une Table ou vue ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image2.png))


Pour inclure un `WHERE` clause dans la `SELECT` instruction, cliquez sur le `WHERE` bouton qui affiche l’ajouter `WHERE` Clause boîte de dialogue (Figure 2). Pour ajouter un paramètre pour limiter les résultats retournés par le `SELECT` de requête, commencez par choisir la colonne à filtrer les données. Ensuite, choisissez l’opérateur à utiliser pour le filtrage (=, &lt;, &lt;=, &gt;, et ainsi de suite). Enfin, choisissez la source de la valeur du paramètre s, telles que de l’état de session ou de la chaîne de requête. Après avoir configuré le paramètre, cliquez sur le bouton Ajouter pour l’inclure dans le `SELECT` requête.

Pour cet exemple, s permettent de retourner uniquement les résultats où les `UnitPrice` valeur est inférieure ou égale à 25,00 $. Par conséquent, choisissez `UnitPrice` à partir de la liste déroulante des colonnes et &lt;= à partir de la liste déroulante opérateur. Lorsque vous utilisez une valeur de paramètre codée en dur (par exemple, $25,00) ou si la valeur du paramètre doit être spécifié par programme, sélectionnez aucun dans la liste déroulante de Source. Ensuite, entrez la valeur de paramètre codée en dur dans la zone de texte valeur 25,00 et terminer le processus en cliquant sur le bouton Ajouter.


[![Limiter les résultats retournés à partir de l’ajouter WHERE Clause boîte de dialogue](using-parameterized-queries-with-the-sqldatasource-cs/_static/image2.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image3.png)

**Figure 2**: limiter les résultats retournés à partir de l’ajout de `WHERE` boîte de dialogue Clause ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image4.png))


Après avoir ajouté le paramètre, cliquez sur OK pour revenir à l’Assistant Configurer la Source de données. Le `SELECT` instruction au bas de l’Assistant doit désormais inclure une `WHERE` clause avec un paramètre nommé `@UnitPrice`:


[!code-sql[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample3.sql)]

> [!NOTE]
> Si vous spécifiez plusieurs conditions dans le `WHERE` clause à partir de l’ajout de `WHERE` boîte de dialogue de Clause, l’Assistant les joint avec la `AND` opérateur. Si vous avez besoin d’inclure un `OR` dans le `WHERE` clause (comme `WHERE UnitPrice <= @UnitPrice OR Discontinued = 1`), vous avez à générer la `SELECT` instruction via l’écran d’instruction SQL personnalisée.


Terminer la configuration le SqlDataSource (cliquez sur Suivant, puis terminer), puis analysez le balisage déclaratif de SqlDataSource s. Le balisage inclut désormais un `<SelectParameters>` collection, qui définit les sources pour les paramètres de la `SelectCommand`.


[!code-aspx[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample4.aspx)]

Lorsque le SqlDataSource s `Select()` méthode est appelée, le `UnitPrice` (25,00) la valeur du paramètre est appliquée à la `@UnitPrice` paramètre dans le `SelectCommand` avant d’être envoyés à la base de données. Il en résulte que seuls ces produits inférieur ou égal à $25,00 sont retournées à partir de la `Products` table. Pour confirmer cela, pour ajouter un contrôle GridView à la page, la lier à cette source de données et affichez la page via un navigateur. Vous devez uniquement voir ces produits répertoriés inférieur ou égal à $25,00, comme la Figure 3 confirme.


[![Uniquement les produits inférieur ou égal à $25,00 sont affichés.](using-parameterized-queries-with-the-sqldatasource-cs/_static/image3.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image5.png)

**Figure 3**: uniquement les produits inférieur ou égal à $25,00 sont affichés ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image6.png))


## <a name="step-2-adding-parameters-to-a-custom-sql-statement"></a>Étape 2 : Ajout de paramètres à une instruction SQL personnalisée

Lors de l’ajout d’une instruction SQL personnalisée, vous pouvez entrer le `WHERE` clause explicitement ou spécifier une valeur dans la cellule de filtre du Générateur de requêtes. Pour illustrer cela, permettent d’afficher uniquement les produits dans un GridView dont le prix est inférieur à un certain seuil s. Commencez par ajouter une zone de texte pour le `ParameterizedQueries.aspx` page pour obtenir cette valeur de seuil de l’utilisateur. Définir la zone de texte s `ID` propriété `MaxPrice`. Ajouter un contrôle bouton et définissez ses `Text` propriété pour afficher les correspondances.

Ensuite, faites glisser un GridView sur la page et choisissez sa balise active pour créer un nouveau SqlDataSource nommé `ProductsFilteredByPriceDataSource`. À partir de l’Assistant Configurer la Source de données, passez à la spécifier une instruction SQL personnalisée ou l’écran de la procédure stockée (voir Figure 4) et entrez la requête suivante :


[!code-sql[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample5.sql)]

Après avoir entré la requête (manuellement ou via le Générateur de requêtes), cliquez sur Suivant.


[![Retourner uniquement les produits inférieure ou égale à une valeur de paramètre](using-parameterized-queries-with-the-sqldatasource-cs/_static/image4.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image7.png)

**Figure 4**: retourner uniquement les produits inférieur ou égal à une valeur de paramètre ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image8.png))


Étant donné que la requête inclut des paramètres, l’écran suivant de l’Assistant nous invite pour la source des valeurs de paramètres. Choisissez le contrôle de la liste de liste déroulante de source de paramètre et `MaxPrice` (le contrôle de zone de texte s `ID` valeur) à partir de la liste déroulante ControlID. Vous pouvez également entrer une valeur par défaut facultative à utiliser dans le cas où l’utilisateur n’a pas entré n’importe quel texte dans le `MaxPrice` zone de texte. Pour l’instant, n’entrez pas de valeur par défaut.


[![La propriété Text de s MaxPrice TextBox est utilisé comme Source du paramètre](using-parameterized-queries-with-the-sqldatasource-cs/_static/image5.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image9.png)

**Figure 5**: le `MaxPrice` s de la zone de texte `Text` propriété est utilisée comme Source de paramètre ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image10.png))


Terminez l’Assistant de configurer la Source de données en cliquant sur Suivant, puis sur Terminer. Le balisage déclaratif pour le GridView, une zone de texte, un bouton et un SqlDataSource suit :


[!code-aspx[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample6.aspx)]

Notez que le paramètre dans le s SqlDataSource `<SelectParameters>` section est un `ControlParameter`, qui inclut des propriétés supplémentaires telles que `ControlID` et `PropertyName`. Lorsque le SqlDataSource s `Select()` méthode est appelée, le `ControlParameter` extrait la valeur de la propriété de contrôle Web spécifiée et l’affecte au paramètre correspondant dans le `SelectCommand`. Dans cet exemple, le `MaxPrice` s Text (propriété) est utilisé comme le `@MaxPrice` la valeur du paramètre.

Prenez une minute pour afficher cette page via un navigateur. Lors de la première visite la page ou chaque fois que le `MaxPrice` zone de texte ne dispose pas d’une valeur sans enregistrements sont affichés dans le GridView.


[![Aucun enregistrement n’est Qu'affiché lorsque le MaxPrice zone de texte est vide](using-parameterized-queries-with-the-sqldatasource-cs/_static/image6.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image11.png)

**Figure 6**: aucun enregistrement sont affichés lorsque le `MaxPrice` zone de texte est vide ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image12.png))


La raison pour laquelle aucun produit n’est affichés est car, par défaut, une chaîne vide pour une valeur de paramètre est convertie en une base de données `NULL` valeur. Depuis la comparaison de `[UnitPrice] <= NULL` a toujours la valeur False, ne renvoie aucun résultat.

Entrer une valeur dans la zone de texte, comme 5,00, puis cliquez sur le bouton Afficher les correspondances. Lors de la publication, le SqlDataSource informe que le GridView qu’une de ses sources de paramètre a été modifié. Par conséquent, le contrôle GridView relie pour le SqlDataSource, affichage de ces produits inférieur ou égal à 5,00 $.


[![Produits inférieur ou égal à 5,00 $ sont affichés.](using-parameterized-queries-with-the-sqldatasource-cs/_static/image7.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image13.png)

**Figure 7**: produits inférieur ou égal à 5,00 $ sont affichés ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image14.png))


## <a name="initially-displaying-all-products"></a>Affichage initial de tous les produits

Au lieu de n’afficher aucun produit lors du premier chargement de la page, nous pouvons souhaiter afficher *tous les* produits. Pour répertorier tous les produits à chaque fois que le `MaxPrice` zone de texte est vide consiste à définir la valeur par défaut de s paramètre à une valeur extrêmement élevée, comme 1000000, étant donné qu’il s improbable que Northwind Traders effectue jamais inventaire dont le prix unitaire dépasse 1 000 000 USD. Toutefois, cette approche imprévoyance et peut ne pas fonctionne dans d’autres situations.

Dans les didacticiels précédents - [paramètres déclaratifs](../basic-reporting/declarative-parameters-cs.md) et [filtrage de maître/détail avec un DropDownList](../masterdetail/master-detail-filtering-with-a-dropdownlist-cs.md) nous étions confrontés à un problème similaire. Notre solution il était à placer cette logique dans la couche de logique métier. Plus précisément, la couche BLL examiné la valeur entrante et, si elle a été `NULL` ou certains réservés valeur, l’appel a été routé vers la méthode de la couche DAL que tous les enregistrements retournés. Si la valeur entrante a une valeur de filtrage normale, un appel à la méthode de la couche DAL qui a exécuté une instruction SQL qui a utilisé un paramétrable `WHERE` clause avec la valeur fournie.

Malheureusement, nous contourner l’architecture lorsque vous utilisez le SqlDataSource. Au lieu de cela, nous avons besoin personnaliser l’instruction SQL pour récupérer intelligemment tous les enregistrements si le `@MaximumPrice` paramètre est `NULL` ou une valeur réservée. Dans cet exercice, permettent de s l’avez ainsi que se le `@MaximumPrice` paramètre est égal à `-1.0`, puis *tous les* les enregistrements doivent être retournées (`-1.0` fonctionne comme une valeur réservée, car aucun produit ne peut avoir une valeur négative `UnitPrice`valeur). Pour ce faire, nous pouvons utiliser l’instruction SQL suivante :


[!code-sql[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample7.sql)]

Cela `WHERE` clause retourne *tous les* enregistre si la `@MaximumPrice` paramètre est égal à `-1.0`. Si la valeur du paramètre n’est pas `-1.0`, uniquement les produits dont `UnitPrice` est inférieure ou égale à la `@MaximumPrice` la valeur du paramètre sont retournés. En définissant la valeur par défaut de la `@MaximumPrice` paramètre `-1.0`, sur le premier chargement de page (ou chaque fois que le `MaxPrice` zone de texte est vide), `@MaximumPrice` aura une valeur de `-1.0` et tous les produits seront affiche.


[![Maintenant tous les produits sont affichés lorsque le MaxPrice zone de texte est vide](using-parameterized-queries-with-the-sqldatasource-cs/_static/image8.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image15.png)

**Figure 8**: désormais tous les produits sont affichés lorsque le `MaxPrice` zone de texte est vide ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image16.png))


Il existe quelques inconvénients à noter avec cette approche. Tout d’abord, sachez que le type de données de paramètre s est déduit par cette utilisation s dans la requête SQL. Si vous modifiez le `WHERE` clause de `@MaximumPrice = -1.0` à `@MaximumPrice = -1`, le runtime traite le paramètre en tant qu’entier. Si vous tentez ensuite d’affecter le `MaxPrice` zone de texte avec une valeur décimale (comme 5,00), une erreur se produit, car elle ne peut pas convertir 5,00 en un entier. Pour résoudre ce problème, soit vous assurer que vous utilisez `@MaximumPrice = -1.0` dans les `WHERE` clause ou, mieux encore, définissez la `ControlParameter` objet s `Type` propriété en nombre décimal.

Deuxièmement, en ajoutant le `OR @MaximumPrice = -1.0` à la `WHERE` clause, le moteur de requête ne peut pas utiliser un index sur `UnitPrice` (en supposant qu’il existe), ce qui entraîne une analyse de table. Cela peut affecter les performances s’il existe un nombre suffisant d’enregistrements dans la `Products` table. Une meilleure approche serait de passer de cette logique à une procédure stockée où un `IF` instruction soit effectue un `SELECT` de requête à partir de la `Products` table sans un `WHERE` clause lorsque tous les enregistrements doivent être retournés ou une dont `WHERE` clause contient uniquement le `UnitPrice` critères, afin qu’un index peut être utilisé.

## <a name="step-3-creating-and-using-parameterized-stored-procedures"></a>Étape 3 : Création et l’utilisation des procédures stockées paramétrées

Les procédures stockées peuvent inclure un ensemble de paramètres d’entrée qui peut ensuite être utilisé dans les instructions SQL définies dans la procédure stockée. Lorsque vous configurez le SqlDataSource pour utiliser une procédure stockée qui accepte des paramètres d’entrée, ces valeurs de paramètre peuvent être spécifiés à l’aide des mêmes techniques comme avec les instructions SQL ad hoc.

Pour illustrer l’utilisation de procédures stockées dans le SqlDataSource, s permettent de créer une nouvelle procédure stockée dans la base de données Northwind nommé `GetProductsByCategory`, qui accepte un paramètre nommé `@CategoryID` et retourne toutes les colonnes des produits dont `CategoryID` la colonne correspond `@CategoryID`. Pour créer une procédure stockée, accédez à l’Explorateur de serveurs et explorez le `NORTHWND.MDF` base de données. (Si vous ne pas afficher l’Explorateur de serveurs, mettez-le en sélectionnant le menu Affichage, puis en sélectionnant l’option de l’Explorateur de serveurs.)

À partir de la `NORTHWND.MDF` de base de données, avec le bouton droit sur le dossier Stored Procedures, sélectionnez Ajouter une nouvelle procédure stockée et entrez la syntaxe suivante :


[!code-sql[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample8.sql)]

Cliquez sur l’icône Enregistrer (ou Ctrl + S) pour enregistrer la procédure stockée. Vous pouvez tester la procédure stockée par dessus à partir du dossier de procédures stockées et choisissez Exécuter. Vous serez invité pour les paramètres de procédure stockée s (`@CategoryID`, dans cette instance), après les résultats seront affichés dans la fenêtre Sortie.


[![Le GetProductsByCategory procédure lorsqu’elle est exécutée une @CategoryID 1](using-parameterized-queries-with-the-sqldatasource-cs/_static/image9.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image17.png)

**Figure 9**: le `GetProductsByCategory` la procédure stockée lorsqu’elle est exécutée une `@CategoryID` 1 ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image18.png))


Permettent d’utiliser cette procédure stockée pour afficher tous les produits dans la catégorie des boissons dans un GridView s. Ajouter un nouveau GridView à la page et la lier à un nouveau SqlDataSource nommé `BeverageProductsDataSource`. Continuer pour spécifier une instruction SQL personnalisée ou l’écran de la procédure stockée, sélectionnez la case de procédure stockée et choisir la `GetProductsByCategory` procédure stockée à partir de la liste déroulante.


[![Sélectionnez le GetProductsByCategory procédure stockée à partir de la liste déroulante](using-parameterized-queries-with-the-sqldatasource-cs/_static/image10.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image19.png)

**La figure 10**: sélectionnez le `GetProductsByCategory` la procédure stockée dans la liste déroulante ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image20.png))


Étant donné que la procédure stockée accepte un paramètre d’entrée (`@CategoryID`), en cliquant sur suivant vous êtes invité à spécifier la source de cette valeur du paramètre s. Les boissons `CategoryID` est 1, par conséquent, conservez la liste déroulante de source paramètre None et entrez 1 dans la zone de texte DefaultValue.


[![Utilisez la valeur codée en dur 1 pour retourner les produits dans la catégorie des boissons](using-parameterized-queries-with-the-sqldatasource-cs/_static/image11.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image21.png)

**Figure 11**: permet de retourner les produits de la catégorie boissons Hard-Coded la valeur 1 ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image22.png))


Comme dans le balisage déclaratif suivant, lors de l’utilisation d’une procédure stockée, le s SqlDataSource `SelectCommand` est définie sur le nom de la procédure stockée et la [ `SelectCommandType` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.sqldatasource.selectcommandtype.aspx) a la valeur `StoredProcedure`, qui indique qui le `SelectCommand` est le nom d’une procédure stockée plutôt que dans une instruction SQL d’ad-hoc.


[!code-aspx[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample9.aspx)]

Tester la page dans un navigateur. Uniquement les produits qui appartiennent à la catégorie boissons sont affichés, bien que *tous les* du produit, les champs sont affichés depuis le `GetProductsByCategory` procédure stockée retourne toutes les colonnes à partir de la `Products` table. Bien entendu, nous avons limiter ou personnaliser les champs affichés dans le contrôle GridView à partir de la boîte de dialogue Modifier les colonnes GridView.


[![Afficher toutes les boissons](using-parameterized-queries-with-the-sqldatasource-cs/_static/image12.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image23.png)

**Figure 12**: toutes les boissons sont affichés ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image24.png))


## <a name="step-4-programmatically-invoking-a-sqldatasource-sselectstatement"></a>Étape 4 : Appeler par programmation un s SqlDataSource`Select()`instruction

Les exemples nous ve rencontrée jusqu'à présent dans le didacticiel précédent et ce didacticiel avoir lié les contrôles SqlDataSource directement à un GridView. Les données de contrôle s SqlDataSource, toutefois, peuvent être par programmation accessibles et sont énumérées dans le code. Cela peut être particulièrement utile lorsque vous devez interroger des données pour l’inspecter, mais ne pas besoin pour l’afficher. Au lieu de devoir écrire tout le code code ADO.NET pour se connecter à la base de données, spécifiez la commande et récupérer les résultats, vous pouvez laisser le SqlDataSource pour gérer ce code monotones.

Pour illustrer l’utilisation des s SqlDataSource données par programme, imaginez que votre supérieur vous approche avec une demande de création d’une page web qui affiche le nom d’une catégorie sélectionnée de façon aléatoire et de ses produits associés. Autrement dit, lorsqu’un utilisateur visite cette page, vous souhaitez choisir une catégorie à partir de façon aléatoire le `Categories` de table et afficher le nom de catégorie répertorie ensuite les produits appartenant à cette catégorie.

Pour ce faire, nous devons deux contrôles SqlDataSource celle de saisir une catégorie aléatoire à partir de la `Categories` table et une autre pour obtenir la catégorie de produits de s. Nous allons construire le SqlDataSource qui Récupère un enregistrement de catégorie aléatoire dans cette étape. Étape 5 examine créant le SqlDataSource qui Récupère les produits de s catégorie.

Commencez par ajouter un SqlDataSource pour `ParameterizedQueries.aspx` et définir son `ID` à `RandomCategoryDataSource`. Le configurer afin qu’il utilise la requête SQL suivante :


[!code-sql[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample10.sql)]

`ORDER BY NEWID()`Retourne les enregistrements triés dans un ordre aléatoire (consultez [Using `NEWID()` pour trier les enregistrements de façon aléatoire](http://www.sqlteam.com/item.asp?ItemID=8747)). `SELECT TOP 1`Retourne le premier enregistrement du jeu de résultats. Rassembler, cette requête renvoie le `CategoryID` et `CategoryName` les valeurs de colonne à partir d’une catégorie unique, sélectionnée aléatoirement.

Pour afficher la catégorie s `CategoryName` valeur, ajoutez un contrôle Web Label à la page, définissez son `ID` propriété `CategoryNameLabel`et d’effacer les son `Text` propriété. Pour récupérer par programme les données à partir d’un contrôle SqlDataSource, nous devons appeler son `Select()` (méthode). Le [ `Select()` méthode](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.sqldatasource.select.aspx) attend un seul paramètre d’entrée de type [ `DataSourceSelectArguments` ](https://msdn.microsoft.com/en-us/library/system.web.ui.datasourceselectarguments.aspx), qui spécifie la façon dont les données doivent être sollicitées avant d’être retourné. Cela peut inclure des instructions sur le tri et filtrage des données et est utilisé par les données que lors du tri ou la pagination des données à partir d’un contrôle SqlDataSource de contrôles Web. Dans notre exemple, cependant, nous ne t besoin les données à modifier avant d’être retourné et par conséquent, passez le `DataSourceSelectArguments.Empty` objet.

Le `Select()` méthode retourne un objet qui implémente `IEnumerable`. Le type retourné dépend de la valeur du contrôle SqlDataSource s [ `DataSourceMode` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.sqldatasource.datasourcemode.aspx). Comme indiqué dans le didacticiel précédent, cette propriété peut être définie à une valeur `DataSet` ou `DataReader`. Si la valeur `DataSet`, le `Select()` méthode retourne un [DataView](https://msdn.microsoft.com/en-us/library/01s96x0z.aspx) objet ; si elle est définie `DataReader`, elle retourne un objet qui implémente [ `IDataReader` ](https://msdn.microsoft.com/en-us/library/system.data.idatareader.aspx). Étant donné que la `RandomCategoryDataSource` SqlDataSource a son `DataSourceMode` propriété `DataSet` (la valeur par défaut), nous travaillerons avec un objet DataView.

Le code suivant illustre comment récupérer les enregistrements à partir de la `RandomCategoryDataSource` SqlDataSource comme un DataView, ainsi que comment lire le `CategoryName` valeur de la colonne à partir de la première ligne de DataView :


[!code-csharp[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample11.cs)]

`randomCategoryView[0]`Retourne le premier `DataRowView` du DataView. `randomCategoryView[0]["CategoryName"]`Retourne la valeur de la `CategoryName` colonne dans cette première ligne. Notez que le DataView est faiblement typé. Pour faire référence à une valeur de colonne, il faut passer le nom de la colonne sous forme de chaîne (nom de catégorie, dans ce cas). Figure 13 illustre le message affiché dans le `CategoryNameLabel` lors de l’affichage de la page. Bien entendu, le nom de catégorie réel affiché est sélectionné aléatoirement par le `RandomCategoryDataSource` SqlDataSource sur chaque visite sur la page (y compris les publications (postback)).


[![Le s sélectionnée aléatoirement de catégorie que nom s’affiche.](using-parameterized-queries-with-the-sqldatasource-cs/_static/image13.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image25.png)

**Figure 13**: s de la catégorie aléatoirement sélectionné nom est affiché ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image26.png))


> [!NOTE]
> Si le SqlDataSource contrôle s `DataSourceMode` propriété avait été définie sur `DataReader`, la valeur de retour à partir de la `Select()` méthode aurait dû être converti en `IDataReader`. Pour lire le `CategoryName` valeur de la colonne à partir de la première ligne, nous d utiliser le code :


[!code-csharp[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample12.cs)]

Avec le SqlDataSource sélectionnant de façon aléatoire une catégorie, nous re prêt à ajouter le contrôle GridView qui répertorie les produits de la catégorie s.

> [!NOTE]
> Au lieu d’utiliser un contrôle Web Label pour afficher le nom de la catégorie s, nous pourrions avoir ajouté un FormView ou DetailsView à la page, la liaison pour le SqlDataSource. À l’aide de l’étiquette, toutefois, nous a permis de découvrir comment appeler par programme la s SqlDataSource `Select()` instruction et utiliser ses données résultantes dans le code.


## <a name="step-5-assigning-parameter-values-programmatically"></a>Étape 5 : Affecter des valeurs de paramètre par programmation

Tous les exemples nous ve rencontrée jusqu'à présent dans ce didacticiel ont utilisé une valeur de paramètre codée en dur ou celui effectuée à partir d’une des sources de paramètres prédéfinis (une valeur de chaîne de requête, un contrôle Web dans la page et ainsi de suite). Toutefois, les paramètres de s contrôle SqlDataSource peuvent également être définis par programme. Pour terminer de notre exemple, nous avons besoin d’un SqlDataSource qui retourne tous les produits appartenant à une catégorie spécifiée. Cette SqlDataSource aura un `CategoryID` paramètre dont la valeur doit être définie en fonction de la `CategoryID` valeur de la colonne retournée par la `RandomCategoryDataSource` SqlDataSource dans le `Page_Load` Gestionnaire d’événements.

Commencez par ajouter un contrôle GridView à la page et la lier à un nouveau SqlDataSource nommé `ProductsByCategoryDataSource`. Comme nous l’avons fait à l’étape 3, configurer le SqlDataSource afin qu’il appelle le `GetProductsByCategory` procédure stockée. Conservez le paramètre source liste déroulante None, mais n’entrez pas de valeur par défaut, comme nous allons définir par programmation la valeur par défaut.


[![Ne spécifiez pas une Source de paramètre ou la valeur par défaut](using-parameterized-queries-with-the-sqldatasource-cs/_static/image14.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image27.png)

**La figure 14**: ne spécifiez pas une Source de paramètre ou la valeur par défaut ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image28.png))


Après avoir terminé l’Assistant SqlDataSource, le balisage déclaratif qui en résulte doit ressembler à ce qui suit :


[!code-aspx[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample13.aspx)]

Nous pouvons attribuer le `DefaultValue` de la `CategoryID` paramètre par programmation dans le `Page_Load` Gestionnaire d’événements :


[!code-csharp[Main](using-parameterized-queries-with-the-sqldatasource-cs/samples/sample14.cs)]

Avec cet ajout, la page contient un GridView qui affiche les produits de la catégorie sélectionnée aléatoirement.


[![Ne spécifiez pas une Source de paramètre ou la valeur par défaut](using-parameterized-queries-with-the-sqldatasource-cs/_static/image15.gif)](using-parameterized-queries-with-the-sqldatasource-cs/_static/image29.png)

**Figure 15**: ne spécifiez pas une Source de paramètre ou la valeur par défaut ([cliquez pour afficher l’image en taille réelle](using-parameterized-queries-with-the-sqldatasource-cs/_static/image30.png))


## <a name="summary"></a>Résumé

Le SqlDataSource permet aux développeurs de page définir des requêtes paramétrables, dont les valeurs de paramètre peuvent être codée en dur, extraites de sources de paramètres prédéfinis ou attribués par programme. Dans ce didacticiel, nous avons vu comment créer une requête paramétrable à partir de l’Assistant Configurer la Source de données pour les requêtes SQL ad hoc et procédures stockées. Nous avons également à l’utilisation de sources de paramètre codée en dur, un contrôle Web comme source de paramètre et par programme en spécifiant la valeur du paramètre.

Comme avec ObjectDataSource, le SqlDataSource fournit également des fonctionnalités pour modifier ses données sous-jacentes. Dans l’étape suivante du didacticiel, nous allons étudier comment définir `INSERT`, `UPDATE`, et `DELETE` instructions avec le SqlDataSource. Une fois ces instructions ont été ajoutées, que nous pouvons utiliser la fonction intégrée insertion, de modification et de suppression de fonctionnalités inhérentes aux contrôles GridView, DetailsView et FormView.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont Scott Clyde, Randell Schmidt et Ken Pespisa. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](querying-data-with-the-sqldatasource-control-cs.md)
[Suivant](inserting-updating-and-deleting-data-with-the-sqldatasource-cs.md)
