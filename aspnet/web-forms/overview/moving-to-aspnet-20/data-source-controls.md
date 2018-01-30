---
uid: web-forms/overview/moving-to-aspnet-20/data-source-controls
title: "Contrôles de Source de données | Documents Microsoft"
author: microsoft
description: "Le contrôle DataGrid dans ASP.NET 1.x marqué une nette amélioration de l’accès aux données dans les applications Web. Toutefois, il n’a pas été aussi conviviale qu’il aurait pu être..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2005
ms.topic: article
ms.assetid: 78fd0e92-f9c6-4e96-a5e9-0375b307a828
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/moving-to-aspnet-20/data-source-controls
msc.type: authoredcontent
ms.openlocfilehash: b1ac7fb62767d61c97fe00338bc0f5087f4863b5
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
<a name="data-source-controls"></a>Contrôles de Source de données
====================
par [Microsoft](https://github.com/microsoft)

> Le contrôle DataGrid dans ASP.NET 1.x marqué une nette amélioration de l’accès aux données dans les applications Web. Toutefois, il n’était pas aussi conviviale qu’il aurait pu être. Il reste nécessaire une quantité importante de code pour obtenir un grand nombre de fonctionnalités utile à partir de celui-ci. C’est le modèle dans tous les domaines d’accès aux données dans 1.x.


Le contrôle DataGrid dans ASP.NET 1.x marqué une nette amélioration de l’accès aux données dans les applications Web. Toutefois, il n’était pas aussi conviviale qu’il aurait pu être. Il reste nécessaire une quantité importante de code pour obtenir un grand nombre de fonctionnalités utile à partir de celui-ci. C’est le modèle dans tous les domaines d’accès aux données dans 1.x.

ASP.NET 2.0 avec répond en partie avec les contrôles de source de données. Les contrôles de source de données dans ASP.NET 2.0 fournissent aux développeurs un modèle déclaratif pour la récupération des données, en affichant les données et modifier des données. Contrôles de source de données vise à fournir une représentation cohérente des données à des contrôles liés aux données, quelle que soit la source de ces données. Au cœur des contrôles de source de données dans ASP.NET 2.0 est la classe abstraite DataSourceControl. La classe DataSourceControl fournit une implémentation de base de l’interface IDataSource et l’interface IListSource, la dernière vous permet d’affecter le contrôle de source de données en tant que la source de données d’un contrôle lié aux données (via la nouvelle propriété DataSourceId décrit plus loin) et d’exposer les données qui y sont sous forme de liste. Chaque liste de données à partir d’un contrôle de source de données est exposée en tant qu’objet DataSourceView. Accès aux instances DataSourceView est fournie par l’interface IDataSource. Par exemple, la méthode GetViewNames retourne une ICollection qui vous permet d’énumérer les DataSourceViews associé à un contrôle de source de données particulier, et la méthode GetView vous permet d’accéder à une instance particulière de la DataSourceView en nom.

Contrôles de source de données n’ont aucune interface utilisateur. Elles sont implémentées en tant que serveur contrôles afin qu’ils peuvent prendre en charge la syntaxe déclarative et afin qu’ils aient accès à l’état de la page si vous le souhaitez. Contrôles de source de données ne rendent pas toutes les balises HTML au client.

> [!NOTE]
> Comme vous le verrez plus tard, il est également mise en cache avantages obtenus à l’aide de contrôles de source de données.


## <a name="storing-connection-strings"></a>Le stockage des chaînes de connexion

Avant de découvrir comment configurer des contrôles de source de données, nous devons couvrent une nouvelle fonctionnalité dans ASP.NET 2.0 concernant les chaînes de connexion. ASP.NET 2.0 introduit une nouvelle section dans le fichier de configuration qui vous permet de faciliter le stockage des chaînes de connexion qui peuvent être lus de manière dynamique lors de l’exécution. Le &lt;connectionStrings&gt; section vous permet de stocker des chaînes de connexion.

L’extrait de code ci-dessous ajoute une nouvelle chaîne de connexion.

[!code-xml[Main](data-source-controls/samples/sample1.xml)]

> [!NOTE]
> Comme avec la &lt;appSettings&gt; section, le &lt;connectionStrings&gt; section apparaît en dehors de la &lt;system.web&gt; section dans le fichier de configuration.


Pour utiliser cette chaîne de connexion, vous pouvez utiliser la syntaxe suivante lors de la définition de l’attribut ConnectionString d’un contrôle serveur.

[!code-aspx[Main](data-source-controls/samples/sample2.aspx)]

Le &lt;connectionStrings&gt; section peut également être chiffrée afin que des informations sensibles ne sont pas exposées. Cette capacité est abordée dans un autre module.

## <a name="caching-data-sources"></a>La mise en cache des Sources de données

Chaque DataSourceControl fournit quatre propriétés de configuration de la mise en cache ; EnableCaching CacheDuration, CacheExpirationPolicy et CacheKeyDependency.

## <a name="enablecaching"></a>EnableCaching

EnableCaching est une propriété booléenne qui détermine la non mise en cache est activée pour le contrôle de source de données.

## <a name="cacheduration-property"></a>Propriété CacheDuration

La propriété CacheDuration définit le nombre de secondes pendant lesquelles le cache reste valide. Si cette propriété **0** , le cache reste valide jusqu'à ce qu’explicitement.

## <a name="cacheexpirationpolicy-property"></a>Propriété de CacheExpirationPolicy

La propriété CacheExpirationPolicy peut avoir la valeur **absolu** ou **décalé**. La valeur absolue signifie que la quantité maximale pendant laquelle les données sont mises en cache est le nombre de secondes spécifié par la propriété CacheDuration. En lui affectant décalé, le délai d’expiration est réinitialisé lors de chaque opération est effectuée.

## <a name="cachekeydependency-property"></a>Propriété CacheKeyDependency

Si une valeur de chaîne est spécifiée pour la propriété CacheKeyDependency, ASP.NET définit une nouvelle dépendance de cache basée sur cette chaîne. Cela vous permet à invalider le cache en modifiant ou en supprimant le CacheKeyDependency simplement de manière explicite.

**Important**: si l’emprunt d’identité est activé et l’accès à la source de données et/ou un contenu de données est basé sur l’identité du client, il est recommandé que la mise en cache désactivée en définissant EnableCaching sur False. Si la mise en cache est activée dans ce scénario et un utilisateur autre que l’utilisateur qui a demandé les données émet une demande, l’autorisation de la source de données n’est pas appliquée. Les données seront simplement être pris en charge à partir du cache.

## <a name="the-sqldatasource-control"></a>Le contrôle SqlDataSource

Le contrôle SqlDataSource permet à un développeur pour accéder aux données stockées dans une base de données relationnelle qui prend en charge ADO.NET. Il peut utiliser le fournisseur System.Data.SqlClient pour accéder à une base de données SQL Server, le fournisseur System.Data.OleDb, le fournisseur System.Data.Odbc ou le fournisseur System.Data.OracleClient pour accéder à Oracle. Par conséquent, le SqlDataSource est certainement pas uniquement utilisé pour accéder aux données dans une base de données SQL Server.

Pour pouvoir utiliser le SqlDataSource, vous simplement fournissez une valeur pour la propriété ConnectionString et spécifier une commande SQL ou procédure stockée. Le contrôle SqlDataSource prend en charge de l’utilisation de l’architecture ADO.NET sous-jacent. Il ouvre la connexion, interroge la source de données ou exécute la procédure stockée, retourne les données, puis ferme la connexion.

> [!NOTE]
> Étant donné que la classe DataSourceControl ferme automatiquement la connexion pour vous, il doit réduire le nombre d’appels de client générée par une fuite de connexions de base de données.


L’extrait de code suivant lie un contrôle DropDownList à un contrôle SqlDataSource à l’aide de la chaîne de connexion qui est stockée dans le fichier de configuration, comme indiqué ci-dessus.

[!code-aspx[Main](data-source-controls/samples/sample3.aspx)]

Comme illustré ci-dessus, la propriété DataSourceMode du SqlDataSource Spécifie le mode pour la source de données. Dans l’exemple ci-dessus, le DataSourceMode correspond à DataReader. Dans ce cas, le SqlDataSource retournera un objet IDataReader à l’aide d’un curseur avant uniquement et en lecture seule. Le type spécifié de l’objet retourné est contrôlé par le fournisseur qui est utilisé. Dans ce cas, j’utilise le fournisseur System.Data.SqlClient comme spécifié dans le &lt;connectionStrings&gt; section du fichier web.config. Par conséquent, l’objet retourné sera de type SqlDataReader. En spécifiant une valeur DataSourceMode du jeu de données, les données peuvent être stockées dans un jeu de données sur le serveur. Ce mode vous permet d’ajouter des fonctionnalités telles que le tri, la pagination, etc. Si j’avais été la liaison de données SqlDataSource à un contrôle GridView, je serait avez choisi le mode de jeu de données. Toutefois, dans le cas d’une liste déroulante, le mode de DataReader est le bon choix.

> [!NOTE]
> Lors de la mise en cache un SqlDataSource ou un AccessDataSource, vous devez définir la propriété DataSourceMode à DataSet. Une exception se produit si vous activez la mise en cache avec un DataSourceMode de DataReader.


## <a name="sqldatasource-properties"></a>Propriétés de SqlDataSource

Voici quelques-unes des propriétés du contrôle SqlDataSource.

### <a name="cancelselectonnullparameter"></a>CancelSelectOnNullParameter

Valeur booléenne qui spécifie si une commande select est annulée si l’un des paramètres est null. La valeur True par défaut.

### <a name="conflictdetection"></a>ConflictDetection

Dans une situation où plusieurs utilisateurs peuvent être mise à jour d’une source de données en même temps, la propriété ConflictDetection détermine le comportement du contrôle SqlDataSource. Cette propriété correspond à une des valeurs de l’énumération ConflictOptions. Ces valeurs sont **CompareAllValues** et **OverwriteChanges**. Si set OverwriteChanges, le dernier à écrire des données dans la source de données remplace toutes les modifications précédentes. Toutefois, si la propriété ConflictDetection a CompareAllValues, les paramètres sont créées pour les colonnes retournées par SelectCommand et paramètres sont également créées pour stocker les valeurs d’origine dans chacune de ces colonnes autorisant le SqlDataSource pour déterminer si les valeurs ont changé depuis l’exécution de SelectCommand.

### <a name="deletecommand"></a>DeleteCommand

Définit ou obtient la chaîne SQL utilisée lors de la suppression de lignes à partir de la base de données. Il peut s’agir d’une requête SQL ou un nom de procédure stockée.

### <a name="deletecommandtype"></a>DeleteCommandType

Définit ou obtient le type de commande de suppression, soit une requête SQL (texte) ou une procédure stockée (StoredProcedure).

### <a name="deleteparameters"></a>DeleteParameters

Retourne les paramètres utilisés par la propriété DeleteCommand de l’objet SqlDataSourceView associé au contrôle SqlDataSource.

### <a name="oldvaluesparameterformatstring"></a>OldValuesParameterFormatString

Cette propriété est utilisée pour spécifier le format des paramètres des valeurs d’origine dans les cas où la propriété ConflictDetection a la valeur CompareAllValues. La valeur par défaut est {0} ce qui signifie que les paramètres de valeur d’origine prend le même nom que le paramètre d’origine. En d’autres termes, si le nom du champ est EmployeeID, le paramètre de valeur d’origine serait @EmployeeID.

### <a name="selectcommand"></a>SelectCommand

Définit ou obtient la chaîne SQL qui est utilisée pour récupérer des données à partir de la base de données. Il peut s’agir d’une requête SQL ou un nom de procédure stockée.

### <a name="selectcommandtype"></a>SelectCommandType

Définit ou obtient le type de commande select, soit une requête SQL (texte) ou une procédure stockée (StoredProcedure).

### <a name="selectparameters"></a>SelectParameters

Retourne les paramètres qui sont utilisés par SelectCommand de l’objet SqlDataSourceView associé au contrôle SqlDataSource.

### <a name="sortparametername"></a>SortParameterName

Obtient ou définit le nom d’un paramètre de procédure stockée qui est utilisé lorsque le tri des données récupérées par le contrôle de source de données. Valide uniquement lorsque la valeur de SelectCommandType StoredProcedure.

### <a name="sqlcachedependency"></a>SqlCacheDependency

Chaîne délimitée par des points-virgules indiquant les bases de données et les tables utilisées dans une dépendance de cache SQL Server. (Les dépendances de cache SQL seront examinés dans un module ultérieur.)

### <a name="updatecommand"></a>UpdateCommand

Définit ou obtient la chaîne SQL qui est utilisée lors de la mise à jour des données dans la base de données. Il peut s’agir d’une requête SQL ou un nom de procédure stockée.

### <a name="updatecommandtype"></a>UpdateCommandType

Définit ou obtient le type de commande de mise à jour, soit une requête SQL (texte) ou une procédure stockée (StoredProcedure).

### <a name="updateparameters"></a>UpdateParameters

Retourne les paramètres utilisés par la propriété UpdateCommand de l’objet SqlDataSourceView associé au contrôle SqlDataSource.

## <a name="the-accessdatasource-control"></a>Le contrôle AccessDataSource

Le contrôle AccessDataSource dérive de la classe de SqlDataSource et est utilisé pour lier des données à une base de données Microsoft Access. La propriété ConnectionString pour le contrôle AccessDataSource est une propriété en lecture seule. Au lieu d’utiliser la propriété ConnectionString, la propriété DataFile est utilisée pour pointer vers la base de données Access comme indiqué ci-dessous.

[!code-aspx[Main](data-source-controls/samples/sample4.aspx)]

Le AccessDataSource toujours la valeur de la base SqlDataSource ProviderName System.Data.OleDb et se connecte à la base de données à l’aide du fournisseur OLE DB Microsoft.Jet.OLEDB.4.0. Vous ne pouvez pas utiliser le contrôle AccessDataSource pour se connecter à une base de données protégé par mot de passe. Si vous devez vous connecter à une base de données protégé par mot de passe, vous devez utiliser le contrôle SqlDataSource.

> [!NOTE]
> Bases de données Access stockées dans le site Web doivent être placés dans l’application\_répertoire de données. ASP.NET n’autorise pas les fichiers de ce répertoire à Explorer. Vous devez accorder des autorisations en lecture et en écriture à l’application le compte de processus\_répertoire de données lors de l’utilisation de bases de données Access.


## <a name="the-xmldatasource-control"></a>Le contrôle XmlDataSource

XmlDataSource est utilisé pour lier des données XML à des contrôles liés aux données. Vous pouvez lier à un fichier XML à l’aide de la propriété DataFile ou vous pouvez lier à une chaîne XML à l’aide de la propriété de données. XmlDataSource expose des attributs XML en tant que champs pouvant être liés. Dans les cas où vous devez lier à des valeurs qui ne sont pas représentés en tant qu’attributs, vous devez utiliser une transformation XSL. Vous pouvez également utiliser des expressions XPath pour filtrer les données XML.

Prenez en compte le fichier XML suivant :

[!code-xml[Main](data-source-controls/samples/sample5.xml)]

Notez que XmlDataSource utilise une propriété XPath de */personnes* pour filtrer uniquement le &lt;personne&gt; nœuds. DropDownList puis-lie à l’attribut de nom à l’aide de la propriété DataTextField.

Le contrôle XmlDataSource est principalement utilisée pour lier des données XML en lecture seule aux données, il est possible de modifier le fichier de données XML. Notez que dans ce cas, insertion automatique, la mise à jour et suppression d’informations dans le fichier XML n’est pas automatique comme il le fait avec d’autres contrôles de source de données. Au lieu de cela, vous devez écrire du code pour modifier manuellement les données à l’aide des méthodes suivantes du contrôle XmlDataSource.

### <a name="getxmldocument"></a>GetXmlDocument

Récupère un objet XmlDocument qui contient le code XML récupéré par le XmlDataSource.

### <a name="save"></a>Enregistrer

Enregistre le XmlDocument en mémoire à la source de données.

Il est important de savoir que la méthode Save fonctionne uniquement lorsque les deux conditions suivantes sont remplies :

1. XmlDataSource est à l’aide de la propriété de fichier de données à lier à un fichier XML au lieu de la propriété de données à lier aux données XML en mémoire.
2. Aucune transformation n’est spécifiée via la propriété TransformFile ou de transformation.

Notez également que la méthode Save peut produire des résultats inattendus lorsqu’elle est appelée par plusieurs utilisateurs simultanément.

## <a name="the-objectdatasource-control"></a>Le contrôle ObjectDataSource

Les contrôles de source de données que nous avons traitée à ce stade sont excellents choix pour les applications de couche 2 où le contrôle de source de données communique directement à la banque de données. Toutefois, de nombreuses applications réelles sont des applications multiniveau où un contrôle de source de données peut-être avoir besoin de communiquer avec un objet métier qui, à son tour, communique avec la couche données. Dans ces situations, ObjectDataSource remplit la facture de façon correcte. ObjectDataSource fonctionne en association avec un objet source. Le contrôle ObjectDataSource crée une instance de l’objet source, appelez la méthode spécifiée et dispose de l’instance d’objet totalité de la portée d’une requête unique, si votre objet possède des méthodes d’instance au lieu de méthodes statiques (Shared en Visual Basic). Par conséquent, votre objet doit être sans état. Autrement dit, votre objet doit acquérir et libérer toutes les ressources requises dans l’étendue d’une demande unique. Vous pouvez contrôler la façon dont l’objet source est créé en gérant l’événement ObjectCreating du contrôle ObjectDataSource. Vous pouvez créer une instance de l’objet source et puis définissez la propriété ObjectInstance de la classe ObjectDataSourceEventArgs à cette instance. Le contrôle ObjectDataSource utilisera l’instance qui est créé dans l’événement ObjectCreating au lieu de créer une instance sur son propre.

Si l’objet source pour un contrôle ObjectDataSource expose des méthodes statiques publiques (Shared en Visual Basic) qui peuvent être appelés pour extraire et modifier des données, un contrôle ObjectDataSource appelle ces méthodes directement. Si un contrôle ObjectDataSource doit créer une instance de l’objet source afin d’effectuer des appels de méthode, l’objet doit inclure un constructeur public qui ne prend aucun paramètre. Le contrôle ObjectDataSource appelle ce constructeur lorsqu’il crée une nouvelle instance de l’objet source.

Si l’objet source ne contient pas un constructeur public sans paramètres, vous pouvez créer une instance de l’objet source qui sera utilisé par le contrôle ObjectDataSource dans l’événement ObjectCreating.

## <a name="specifying-object-methods"></a>Spécification des méthodes d’objet

L’objet source pour un contrôle ObjectDataSource peut contenir tout nombre de méthodes qui sont utilisées pour sélectionner, insérer, mettre à jour ou supprimer des données. Ces méthodes sont appelées par le contrôle ObjectDataSource basé sur le nom de la méthode, telles qu’identifiées à l’aide de l’objet SelectMethod, InsertMethod, UpdateMethod ou DeleteMethod propriété du contrôle ObjectDataSource. L’objet source peut également inclure une méthode SelectCount facultatif, qui est identifiée par le contrôle ObjectDataSource à l’aide de la propriété SelectCountMethod, qui retourne le nombre total d’objets à la source de données. Le contrôle ObjectDataSource appelle la méthode SelectCount après l’appel d’une méthode Select pour récupérer le nombre total d’enregistrements de la source de données à utiliser lors de la pagination.

## <a name="lab-using-data-source-controls"></a>Lab à l’aide de contrôles de Source de données

## <a name="exercise-1---displaying-data-with-the-sqldatasource-control"></a>Exercice 1 : affichage des données avec le contrôle SqlDataSource

L’exercice suivant utilise le contrôle SqlDataSource pour se connecter à la base de données Northwind. Il suppose que vous avez accès à la base de données Northwind sur une instance de SQL Server 2000.

1. Créez un site web ASP.NET.
2. Ajouter un nouveau fichier web.config.

    1. Avec le bouton droit sur le projet dans l’Explorateur de solutions, puis cliquez sur Ajouter un nouvel élément.
    2. Choisissez le fichier de Configuration Web à partir de la liste des modèles et cliquez sur Ajouter.
3. Modifier la &lt;connectionStrings&gt; section comme suit : 

    [!code-aspx[Main](data-source-controls/samples/sample6.aspx)]
4. Basculez en mode Code et ajoutez un attribut ConnectionString et SelectCommand un attribut le &lt;asp : SqlDataSource&gt; contrôle comme suit : 

    [!code-aspx[Main](data-source-controls/samples/sample7.aspx)]
5. Mode Design, ajoutez un nouveau contrôle GridView.
6. Dans la liste déroulante Choisir la Source de données dans le menu Tâches GridView, choisissez SqlDataSource1.
7. Avec le bouton droit sur Default.aspx et choisissez Afficher dans le navigateur à partir du menu. Lorsque vous êtes invité à enregistrer, cliquez sur Oui.
8. Le contrôle GridView affiche les données de la table Products.

## <a name="exercise-2---editing-data-with-the-sqldatasource-control"></a>Exercice 2 : modification des données avec le contrôle SqlDataSource

L’exercice suivant montre comment lier des données DropDownList contrôler à l’aide de la syntaxe déclarative et permet de modifier les données présentées dans le contrôle DropDownList.

1. Dans ce mode, supprimez le contrôle GridView Default.aspx. 

    **Important**: conservez le contrôle SqlDataSource sur la page.
2. Ajouter un contrôle DropDownList vers Default.aspx.
3. Basculez en mode Source.
4. Ajoutez un attribut DataSourceId DataTextField et DataValueField à la &lt;asp : DropDownList&gt; contrôle comme suit : 

    [!code-aspx[Main](data-source-controls/samples/sample8.aspx)]
5. Enregistrez Default.aspx et l’afficher dans le navigateur. Notez que DropDownList contient tous les produits à partir de la base de données Northwind.
6. Fermez le navigateur.
7. Dans la vue de Source de Default.aspx, ajoutez un nouveau contrôle de zone de texte sous le contrôle DropDownList. Modifier la propriété ID de la zone de texte à txtProductName.
8. Sous le contrôle de zone de texte, ajoutez un contrôle bouton. Modifier la propriété ID du bouton btnUpdate et de la propriété de texte à **nom de produit de mise à jour**.
9. Dans la vue de Source de Default.aspx, ajoutez une propriété UpdateCommand et deux UpdateParameters nouveau comme suit à la balise SqlDataSource : 

    [!code-aspx[Main](data-source-controls/samples/sample9.aspx)]

    > [!NOTE]
    > Notez qu’il existe que deux paramètres (ProductName et ProductID) ajoutés dans ce code de mise à jour. Ces paramètres sont mappés à la propriété Text de la zone de texte txtProductName et la propriété SelectedValue de la ddlProducts DropDownList.
10. Basculez en mode Design et double-cliquez sur le contrôle bouton pour ajouter un gestionnaire d’événements.
11. Ajoutez le code suivant à la btnUpdate\_cliquez sur le code : 

    [!code-csharp[Main](data-source-controls/samples/sample10.cs)]
12. Avec le bouton droit sur Default.aspx, puis choisissez pour l’afficher dans le navigateur. Lorsque vous êtes invité à enregistrer toutes les modifications, cliquez sur Oui.
13. ASP.NET 2.0 les classes partielles permettent de compilation pendant l’exécution. Il n’est pas nécessaire générer une application afin de voir les modifications de code prennent effet.
14. Sélectionnez un produit dans la liste déroulante.
15. Entrez un nouveau nom pour le produit sélectionné dans la zone de texte, puis sur le bouton de mise à jour.
16. Le nom du produit est mis à jour dans la base de données.

## <a name="exercise-3-using-the-objectdatasource-control"></a>Exercice 3 à l’aide du contrôle ObjectDataSource

Cet exercice va vous montrer comment utiliser le contrôle ObjectDataSource et un objet source pour interagir avec la base de données Northwind.

1. Avec le bouton droit sur le projet dans l’Explorateur de solutions, puis cliquez sur Ajouter un nouvel élément.
2. Sélectionnez le formulaire Web dans la liste des modèles. Remplacez le nom object.aspx et cliquez sur Ajouter.
3. Avec le bouton droit sur le projet dans l’Explorateur de solutions, puis cliquez sur Ajouter un nouvel élément.
4. Sélectionnez la classe dans la liste des modèles. Remplacez le nom de la classe NorthwindData.cs et cliquez sur Ajouter.
5. Cliquez sur Oui lorsque vous êtes invité à ajouter la classe à l’application\_dossier de Code.
6. Ajoutez le code suivant au fichier NorthwindData.cs : 

    [!code-csharp[Main](data-source-controls/samples/sample11.cs)]
7. Ajoutez le code suivant à la vue de Source de object.aspx : 

    [!code-aspx[Main](data-source-controls/samples/sample12.aspx)]
8. Enregistrez tous les fichiers et parcourir object.aspx.
9. Interagir avec l’interface en affichage des détails, modification des employés, ajouter des employés et suppression des employés.
