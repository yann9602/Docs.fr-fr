---
uid: web-forms/overview/older-versions-getting-started/continuing-with-ef/using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started
title: "À l’aide d’Entity Framework 4.0 et du contrôle ObjectDataSource, partie 1 : prise en main | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels s’appuie sur l’application web de Contoso University créé par la prise en main de la série de didacticiels Entity Framework. Si v..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/26/2011
ms.topic: article
ms.assetid: 244278c1-fec8-4255-8a8a-13bde491c4f5
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/continuing-with-ef/using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started
msc.type: authoredcontent
ms.openlocfilehash: 6f93d6033b68773507d624125936f0a69777e2b7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-the-entity-framework-40-and-the-objectdatasource-control-part-1-getting-started"></a>À l’aide d’Entity Framework 4.0 et du contrôle ObjectDataSource, partie 1 : prise en main
====================
Par [Tom Dykstra](https://github.com/tdykstra)

> Cette série de didacticiels s’appuie sur l’application web de l’université Contoso qui est créée par le [mise en route avec l’Entity Framework 4.0](../getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-1.md) série de didacticiels. Si vous n’avez pas terminé les didacticiels antérieures, comme point de départ pour ce didacticiel vous pouvez [télécharger l’application](https://code.msdn.microsoft.com/ASPNET-Web-Forms-97f8ee9a) que vous devriez avoir créé. Vous pouvez également [télécharger l’application](https://code.msdn.microsoft.com/ASPNET-Web-Forms-6c7197aa) qui est créé par la série de didacticiels terminée.
> 
> L’exemple d’application web Contoso University montre comment créer des applications Web Forms ASP.NET à l’aide de l’Entity Framework 4.0 et Visual Studio 2010. L’exemple d’application est un site Web pour une université fictif de Contoso. Il inclut des fonctionnalités telles que leur admission d’étudiant, la création de cours et les affectations de formateur.
> 
> Le didacticiel présente des exemples dans c#. Le [exemple téléchargeable](https://code.msdn.microsoft.com/ASPNET-Web-Forms-6c7197aa) contient le code en c# et Visual Basic.
> 
> ## <a name="database-first"></a>Tout d’abord la base de données
> 
> Il existe trois méthodes que vous pouvez utiliser des données dans Entity Framework : *Database First*, *Model First*, et *Code First*. Ce didacticiel est pour la première base de données. Pour plus d’informations sur les différences entre ces flux de travail et les conseils sur la façon de choisir la meilleure pour votre scénario, consultez [flux de travail de développement Entity Framework](https://msdn.microsoft.com/en-us/library/ms178359.aspx#dbfmfcf).
> 
> ## <a name="web-forms"></a>Web Forms
> 
> Comme la série de mise en route, cette série de didacticiels utilise le modèle Web Forms ASP.NET et suppose que vous savez comment travailler avec ASP.NET Web Forms dans Visual Studio. Si vous ne faites pas, consultez [prise en main de Web Forms ASP.NET 4.5](../../getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview.md). Si vous préférez travailler avec l’infrastructure ASP.NET MVC, consultez [mise en route avec Entity Framework à l’aide d’ASP.NET MVC](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).
> 
> ## <a name="software-versions"></a>Versions du logiciel
> 
> | **Le didacticiel** | **Fonctionne également avec** |
> | --- | --- |
> | Windows 7 | Windows 8 |
> | Visual Studio 2010 | Visual Studio 2010 Express pour le Web. Le didacticiel n’a pas été testé avec les versions ultérieures de Visual Studio. Il existe de nombreuses différences dans les sélections de menu, les boîtes de dialogue et les modèles. |
> | .NET 4 | .NET 4.5 est compatible avec le .NET 4, mais le didacticiel n’a pas été testé avec le .NET 4.5. |
> | Entity Framework 4 | Le didacticiel n’a pas été testé avec les versions ultérieures d’Entity Framework. Depuis Entity Framework 5, EF utilise par défaut le `DbContext API` qui a été introduite avec EF 4.1. Le contrôle EntityDataSource a été conçu pour utiliser le `ObjectContext` API. Pour plus d’informations sur l’utilisation du contrôle EntityDataSource contrôler avec la `DbContext` API, consultez [ce billet de blog](https://blogs.msdn.com/b/webdev/archive/2012/09/13/how-to-use-the-entitydatasource-control-with-entity-framework-code-first.aspx). |
> 
> ## <a name="questions"></a>Questions
> 
> Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [forum de ASP.NET Entity Framework](https://forums.asp.net/1227.aspx), le [Entity Framework et LINQ to forum d’entités](https://social.msdn.microsoft.com/forums/en-US/adodotnetentityframework/threads/), ou [ StackOverflow.com](http://stackoverflow.com/).


Le `EntityDataSource` contrôle vous permet de créer une application très rapidement, mais elle requiert en général vous permet de conserver une quantité importante de logique métier et logique d’accès aux données dans votre *.aspx* pages. Si vous pensez que votre application à croître en complexité et requièrent une maintenance, vous pouvez investir plus de temps de développement au préalable afin de créer un *MULTICOUCHE* ou *en couche* structure de l’application qui est plus facile à gérer. Pour implémenter cette architecture, vous séparez la couche de présentation à partir de la couche de logique métier (BLL) et la couche d’accès aux données (DAL). La première consiste à implémenter cette structure à utiliser le `ObjectDataSource` contrôler au lieu du `EntityDataSource` contrôle. Lorsque vous utilisez la `ObjectDataSource` contrôle, vous implémentez votre propre code d’accès aux données, puis pour appeler dans *.aspx* pages à l’aide d’un contrôle contenant la plupart de ces fonctionnalités que les autres contrôles de source de données. Cela vous permet de combiner les avantages d’une approche multiniveau avec les avantages de l’utilisation d’un contrôle Web Forms pour accéder aux données.

Le `ObjectDataSource` contrôle vous offre plus de souplesse, d’autres façons. Étant donné que vous écrivez votre propre code d’accès aux données, il est plus facile de plus que lire, insérer, mettre à jour ou supprimer un type d’entité spécifique, qui sont les tâches qui le `EntityDataSource` contrôle est conçue pour effectuer. Par exemple, vous pouvez effectuer l’enregistrement chaque fois qu’une entité est mis à jour, archiver des données chaque fois qu’une entité est supprimée, ou automatiquement vérification et mise à jour des données liées en fonction des besoins lors de l’insertion d’une ligne avec une valeur de clé étrangère.

## <a name="business-logic-and-repository-classes"></a>La logique métier et des Classes de référentiel

Un `ObjectDataSource` fonctionnement du contrôle en appelant une classe que vous créez. La classe inclut des méthodes qui récupèrent et mettre à jour des données, et vous fournir les noms de ces méthodes pour la `ObjectDataSource` contrôle dans le balisage. Pendant le rendu et le traitement de publication (postback), le `ObjectDataSource` appelle les méthodes que vous avez spécifiés.

Outre les opérations CRUD de base, la classe que vous créez pour l’utiliser avec le `ObjectDataSource` contrôle ne doivent pas exécuter la logique métier lors de la `ObjectDataSource` lit ou met à jour des données. Par exemple, lorsque vous mettez à jour un service, vous devrez peut-être valider que sans autres départements ont le même administrateur, car une personne ne peut pas être administrateur de plus d’un service.

Dans certains `ObjectDataSource` documentation, telles que la [vue d’ensemble de la classe de ObjectDataSource](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.objectdatasource.aspx), le contrôle appelle une classe appelée un *objet métier* qui inclut une logique métier et logique d’accès aux données . Dans ce didacticiel, vous allez créer des classes distinctes pour la logique métier et logique d’accès aux données. La classe qui encapsule la logique d’accès aux données est appelée un *référentiel*. La classe de logique métier inclut à la fois des méthodes de logique métier et des méthodes d’accès aux données, mais les méthodes d’accès aux données appellent le référentiel pour effectuer des tâches d’accès aux données.

Vous allez également créer une couche d’abstraction entre votre BLL et la couche DAL qui facilite l’unité automatisée de la couche BLL de test. Cette couche d’abstraction est implémentée par la création d’une interface et l’utilisation de l’interface lorsque vous instanciez le référentiel dans la classe de logique métier. Cela rend possible pour fournir la logique métier de classe avec une référence à un objet qui implémente l’interface de référentiel. Pour un fonctionnement normal, vous fournissez un objet de référentiel qui fonctionne avec Entity Framework. Pour le test, vous fournissez un objet de référentiel qui fonctionne avec les données stockées d’une manière qui vous pouvez de manipuler facilement, tels que des variables de classe définis en tant que collections.

L’illustration suivante montre la différence entre une classe de logique métier qui inclut la logique d’accès aux données sans un référentiel et une qui utilise un référentiel.

[![Image05](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image2.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image1.png)

Vous commencerez par créer des pages web dans lequel le `ObjectDataSource` contrôle est lié directement à un référentiel, car elle effectue uniquement les tâches de base d’accès aux données. Dans le didacticiel suivant vous créez une classe de logique métier avec la logique de validation et lier le `ObjectDataSource` contrôle à cette classe et non à la classe de référentiel. Vous allez également créer des tests unitaires pour la logique de validation. Dans le troisième didacticiel de cette série vous allez ajouter un tri et filtrage des fonctionnalités à l’application.

Les pages que vous créez dans ce didacticiel fonctionnent avec les `Departments` jeu d’entités du modèle de données que vous avez créé dans le [série de didacticiels mise en route](../getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-1.md).

[![Image01](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image4.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image3.png)

[![Image02](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image6.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image5.png)

## <a name="updating-the-database-and-the-data-model"></a>Mise à jour la base de données et le modèle de données

Vous commencerez ce didacticiel en apportant des modifications de deux à la base de données, qui nécessitent des modifications correspondantes apportées au modèle de données que vous avez créé dans le [prise en main de l’Entity Framework et les Web Forms](../getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-1.md) didacticiels. Dans un de ces didacticiels, vous avez modifié dans le concepteur manuellement pour synchroniser le modèle de données avec la base de données après une modification de la base de données. Dans ce didacticiel, vous allez utiliser le Concepteur de la **mise à jour de base de données Model** outil pour mettre à jour le modèle de données automatiquement.

### <a name="adding-a-relationship-to-the-database"></a>Ajout d’une relation à la base de données

Dans Visual Studio, ouvrez l’application web de Contoso University vous avez créé dans le [prise en main de l’Entity Framework et les Web Forms](../getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-1.md) série de didacticiels, puis ouvrez le `SchoolDiagram` diagramme de la base de données.

Si vous examinez la `Department` table dans le diagramme de la base de données, vous verrez qu’il possède un `Administrator` colonne. Cette colonne est une clé étrangère pour la `Person` table, mais aucune relation de clé étrangère est définie dans la base de données. Vous devez créer la relation et mis à jour le modèle de données Entity Framework peut gérer cette relation.

Dans le diagramme de la base de données, cliquez sur le `Department` de table, puis sélectionnez **relations**.

[![Image80](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image8.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image7.png)

Dans le **relations de clé étrangère** boîte, cliquez sur **ajouter**, puis cliquez sur le bouton de sélection pour **spécification de Tables et colonnes**.

[![Image81](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image10.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image9.png)

Dans le **Tables et colonnes** boîte de dialogue zone, de définir la table de clé primaire et de champ à `Person` et `PersonID`et définir la table de clé étrangère et champ à `Department` et `Administrator`. (Dans ce cas, le nom de la relation pourra `FK_Department_Department` à `FK_Department_Person`.)

[![Image82](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image12.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image11.png)

Cliquez sur **OK** dans les **Tables et colonnes** , cliquez sur **fermer** dans les **relations de clé étrangère** zone et enregistrez les modifications. Si vous êtes invité à indiquer si vous souhaitez enregistrer le `Person` et `Department` tables, cliquez sur **Oui**.

> [!NOTE]
> Si vous avez supprimé `Person` les lignes qui correspondent à des données qui se trouve déjà dans la `Administrator` colonne, vous ne pourrez enregistrer cette modification. Dans ce cas, utilisez l’éditeur de table de **l’Explorateur de serveurs** pour vous assurer que le `Administrator` valeur dans chaque `Department` ligne contient l’ID d’un enregistrement existe réellement dans le `Person` table.
> 
> Une fois que vous enregistrez la modification, vous ne serez pas en mesure de supprimer des lignes de la `Person` table si celle-ci est un administrateur de service. Dans une application de production, vous serez fournir un message d’erreur lorsqu’une contrainte de base de données empêche une suppression, ou vous devez spécifier une suppression en cascade. Pour obtenir un exemple montrant comment spécifier une suppression en cascade, consultez [Entity Framework et ASP.NET – mise en route démarré Part 2](../getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-2.md).


### <a name="adding-a-view-to-the-database"></a>Ajout d’une vue à la base de données

Dans la nouvelle *Departments.aspx* page que vous allez créer, que vous souhaitez fournir une liste déroulante des instructeurs, avec les noms de format « nom, prénom » afin que les utilisateurs peuvent sélectionner des administrateurs de service. Pour le rendre plus facile pour ce faire, vous allez créer une vue dans la base de données. La vue se compose de données nécessaires à la liste déroulante : le nom complet (correct) et la clé d’enregistrement.

Dans **l’Explorateur de serveurs**, développez *School.mdf*, avec le bouton droit le **vues** dossier, puis sélectionnez **ajouter une nouvelle vue**.

[![Image06](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image14.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image13.png)

Cliquez sur **fermer** lors de la **ajouter une Table** boîte de dialogue s’affiche et collez l’instruction SQL suivante dans le volet SQL :

[!code-sql[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample1.sql)]

Enregistrez la vue en tant que `vInstructorName`.

### <a name="updating-the-data-model"></a>Mise à jour le modèle de données

Dans le *DAL* dossier, ouvrez le *SchoolModel.edmx* de fichiers, cliquez sur l’aire de conception, puis sélectionnez **modèle de mise à jour à partir de la base de données**.

[![Image07](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image16.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image15.png)

Dans le **choisir vos objets de base de données** boîte de dialogue, sélectionnez le **ajouter** onglet et sélectionnez la vue que vous venez de créer.

[![Image08](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image18.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image17.png)

Cliquez sur **Terminer**.

Dans le concepteur, vous voyez que l’outil a créé un `vInstructorName` entité et une nouvelle association entre le `Department` et `Person` entités.

[![Image13](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image20.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image19.png)

> [!NOTE]
> Dans le **sortie** et **liste d’erreurs** windows, vous pouvez voir un message d’avertissement vous informant que l’outil créé automatiquement un principal de clé pour le nouveau `vInstructorName` vue. Ce comportement est normal.


Lorsque vous faites référence à la nouvelle `vInstructorName` entité dans le code, que vous souhaitez utiliser la convention de base de données d’en ajoutant le préfixe « v » en minuscules à celui-ci. Par conséquent, vous allez renommer l’entité et le jeu d’entités dans le modèle.

Ouvrez le **modèle Browser**. Vous consultez `vInstructorName` répertorié comme un type d’entité et une vue.

[![Image14](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image22.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image21.png)

Sous **SchoolModel** (pas **SchoolModel.Store**), avec le bouton droit **vInstructorName** et sélectionnez **propriétés**. Dans le **propriétés** fenêtre, modification le **nom** à la propriété « InstructorName » et de modifier le **entité définir un nom** propriété « InstructorNames ».

[![Image15](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image24.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image23.png)

Enregistrez et fermez le modèle de données et puis regénérez le projet.

## <a name="using-a-repository-class-and-an-objectdatasource-control"></a>À l’aide d’une classe de l’espace de stockage et un contrôle ObjectDataSource

Créer un nouveau fichier de classe dans le *DAL* dossier, nommez-le *SchoolRepository.cs*et remplacez le code existant par le code suivant :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample2.cs)]

Ce code fournit un seul `GetDepartments` méthode qui retourne toutes les entités dans le `Departments` jeu d’entités. Car vous savez que vous voulez accéder la `Person` propriété de navigation pour chaque ligne retournée, vous spécifiez hâtif le chargement de cette propriété à l’aide de la `Include` (méthode). La classe implémente également la `IDisposable` interface pour vous assurer que la connexion de base de données est libérée lorsque l’objet est supprimé.

> [!NOTE]
> Une pratique courante consiste à créer une classe de référentiel pour chaque type d’entité. Dans ce didacticiel, une classe de référentiel pour plusieurs types d’entités est utilisée. Pour plus d’informations sur le modèle de référentiel, consultez les billets dans [blog de l’équipe Entity Framework](https://blogs.msdn.com/b/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx) et [blog de Julie Lerman](http://thedatafarm.com/blog/data-access/agile-ef4-repository-part-3-fine-tuning-the-repository/).


Le `GetDepartments` méthode retourne un `IEnumerable` objet plutôt qu’une `IQueryable` objet afin de garantir que la collection retournée est utilisable, même après que l’objet de référentiel lui-même est supprimé. Un `IQueryable` objet peut entraîner la base de données chaque fois qu’il est accessible, mais l’objet de référentiel peut être supprimé au moment où un contrôle lié aux données tente de rendre les données. Vous pouvez retourner un autre type de collection, comme un `IList` au lieu de l’objet une `IEnumerable` objet. Toutefois, en retournant un `IEnumerable` objet garantit que vous pouvez effectuer des tâches de traitement des listes en lecture seule par défaut tels que `foreach` boucles et les requêtes LINQ, mais vous ne peut pas ajouter ou supprimer des éléments dans la collection, ce qui implique que ces modifications serait conservés dans la base de données.

Créer un *Departments.aspx* page qui utilise le *Site.Master* page maître et ajoutez le balisage suivant dans le `Content` contrôle nommé `Content2`:

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample3.aspx)]

Cette balise crée un `ObjectDataSource` contrôle qui utilise la classe d’espace de stockage que vous venez de créer, et un `GridView` contrôle pour afficher les données. Le `GridView` contrôle spécifie **modifier** et **supprimer** commandes, mais vous n’avez pas ajouté du code pour prendre en charge encore.

Utilisent de plusieurs colonnes `DynamicField` contrôle afin que vous pouvez tirer parti des fonctionnalités de mise en forme et de validation automatique des données. Pour qu’elles fonctionnent, vous devez appeler la `EnableDynamicData` méthode dans le `Page_Init` Gestionnaire d’événements. (`DynamicControl` contrôles ne sont pas utilisés dans le `Administrator` , car ils ne fonctionnent pas avec les propriétés de navigation.)

Le `Vertical-Align="Top"` attributs sera importants plus tard lorsque vous ajoutez une colonne qui comprend un bloc imbriqué `GridView` contrôle à la grille.

Ouvrez le *Departments.aspx.cs* de fichiers et ajoutez le code suivant `using` instruction :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample4.cs)]

Puis ajoutez le gestionnaire suivant de la page `Init` événement :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample5.cs)]

Dans le *DAL* dossier, créez un nouveau fichier de classe nommé *Department.cs* et remplacez le code existant par le code suivant :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample6.cs)]

Ce code ajoute des métadonnées au modèle de données. Il spécifie que la `Budget` propriété de la `Department` entité représente en fait la devise bien que son type de données est `Decimal`, et spécifie que la valeur doit être comprise entre 0 et le format 1,000,000.00 $. Il spécifie également que le `StartDate` propriété doit être mis en forme comme une date au format mm/jj/aaaa.

Exécutez le *Departments.aspx* page.

[![Image01](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image26.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image25.png)

Notez que bien que vous ne spécifiez pas une chaîne de format dans le *Departments.aspx* balisage de page pour le **Budget** ou **Date de début** par défaut des colonnes, devise et la date mise en forme a été appliqué à l’aide du `DynamicField` des contrôles, en utilisant les métadonnées que vous avez fournies dans le *Department.cs* fichier.

## <a name="adding-insert-and-delete-functionality"></a>Ajout d’insertion et de fonctionnalités de suppression

Ouvrez *SchoolRepository.cs*, ajoutez le code suivant pour créer un `Insert` (méthode) et un `Delete` (méthode). Le code inclut également une méthode nommée `GenerateDepartmentID` qui calcule la valeur de clé suivante enregistrement disponible pour une utilisation par le `Insert` (méthode). Cela est nécessaire car la base de données n’est pas configuré pour cela automatiquement pour calculer le `Department` table.

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample7.cs)]

### <a name="the-attach-method"></a>La méthode d’attachement

Le `DeleteDepartment` les appels de méthode le `Attach` méthode afin de rétablir le lien qui est conservé dans le Gestionnaire d’état du contexte de l’objet objet entre l’entité dans la mémoire et de la base de données de ligne qu’il représente. Cela doit se produire avant les appels de méthode le `SaveChanges` (méthode).

Le terme *contexte de l’objet* fait référence à la classe Entity Framework qui dérive de la `ObjectContext` classe que vous utilisez pour accéder à vos jeux d’entités et les entités. Dans le code pour ce projet, la classe est nommée `SchoolEntities`, et une instance de celui-ci est toujours nommée `context`. Le contexte d’objet *Gestionnaire d’état objet* est une classe qui dérive de la `ObjectStateManager` classe. Le contact de l’objet utilise le Gestionnaire d’état objet pour stocker les objets d’entité et de savoir si chacun d’eux est synchronisée avec sa ligne correspondante de la table ou des lignes dans la base de données.

Lorsque vous lisez une entité, le contexte de l’objet stocke dans le Gestionnaire d’état objet et effectue le suivi de cette représentation de l’objet soit synchronisée avec la base de données. Par exemple, si vous modifiez une valeur de propriété, un indicateur est défini pour indiquer que la propriété que vous avez modifié n’est plus synchronisée avec la base de données. Lorsque vous appelez le `SaveChanges` (méthode), le contexte de l’objet sait comment procéder dans la base de données, car le Gestionnaire d’état objet sait exactement ce qui est différent entre l’état actuel de l’entité et de l’état de la base de données.

Toutefois, ce processus généralement ne fonctionne pas dans une application web, car l’instance de contexte d’objet qui lit une entité, ainsi que tous les éléments dans le Gestionnaire d’état de son objet, est supprimé après le rendu d’une page. L’instance de contexte d’objet qui doit appliquer les modifications est une autre qui est instanciée pour le traitement de publication (postback). Dans le cas de la `DeleteDepartment` (méthode), la `ObjectDataSource` contrôle ré-crée la version d’origine de l’entité à partir de valeurs de l’état d’affichage, mais il est recréé `Department` entité n’existe pas dans le Gestionnaire d’état de l’objet. Si vous avez appelé la `DeleteObject` méthode sur cette entité recréée, l’appel échoue car le contexte de l’objet ne sait pas si l’entité est synchronisée avec la base de données. Toutefois, l’appel du `Attach` méthode établit à nouveau le même suivi entre l’entité recréée et les valeurs dans la base de données qui a été initialement effectué automatiquement lors de la lecture de l’entité dans une instance antérieure de contexte de l’objet.

Il existe des cas, lorsque vous ne souhaitez pas que le contexte de l’objet pour effectuer le suivi des entités dans le Gestionnaire d’état de l’objet, et vous pouvez définir des indicateurs pour l’empêcher d’y parvenir. Exemples figurent dans les didacticiels plus loin dans cette série.

### <a name="the-savechanges-method"></a>La méthode SaveChanges

Cette classe de données de référentiel simple illustre les principes fondamentaux de la façon d’effectuer des opérations CRUD. Dans cet exemple, le `SaveChanges` est appelée immédiatement après chaque mise à jour. Dans une application de production, vous souhaiterez appeler le `SaveChanges` méthode à partir d’une méthode distincte pour vous donner plus de contrôle sur lorsque la base de données est mise à jour. (À la fin de l’étape suivante du didacticiel vous trouverez un lien vers un livre blanc qui présente l’unité du modèle de travail qui est une approche pour la coordination des mises à jour.) Notez également que, dans l’exemple, le `DeleteDepartment` méthode n’inclut pas de code pour la gestion des conflits d’accès concurrentiel ; le code à cet effet est ajoutée dans un didacticiel plus loin dans cette série.

### <a name="retrieving-instructor-names-to-select-when-inserting"></a>La récupération des noms de formateur à sélectionner lors de l’insertion

Les utilisateurs doivent être en mesure de sélectionner un administrateur à partir d’une liste de formateurs dans une liste déroulante lors de la création de nouveaux services. Par conséquent, ajoutez le code suivant à *SchoolRepository.cs* pour créer une méthode pour récupérer la liste des formateurs à l’aide de la vue que vous avez créé précédemment :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample8.cs)]

### <a name="creating-a-page-for-inserting-departments"></a>Création d’une Page pour insérer des services

Créer un *DepartmentsAdd.aspx* page qui utilise le *Site.Master* page et ajoutez le balisage suivant dans le `Content` contrôle nommé `Content2`:

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample9.aspx)]

Ce balisage crée deux `ObjectDataSource` contrôles pour insérer de nouveaux `Department` entités et un pour la récupération des noms de formateur pour la `DropDownList` contrôle qui permet de sélectionner des administrateurs de service. Le balisage crée un `DetailsView` de contrôle pour entrer de nouveaux services, qui spécifie un gestionnaire pour du contrôle `ItemInserting` événement afin que vous puissiez définir le `Administrator` valeur de clé étrangère. À la fin est un `ValidationSummary` contrôle pour afficher les messages d’erreur.

Ouvrez *DepartmentsAdd.aspx.cs* et ajoutez le code suivant `using` instruction :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample10.cs)]

Ajoutez les méthodes et la variable de classe suivante :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample11.cs)]

Le `Page_Init` méthode active des fonctionnalités Dynamic Data. Le gestionnaire pour le `DropDownList` du contrôle `Init` événement enregistre une référence au contrôle et le Gestionnaire de la `DetailsView` du contrôle `Inserting` événement utilise cette référence pour obtenir la `PersonID` valeur du formateur sélectionné et mise à jour le `Administrator` une propriété de clé étrangère de la `Department` entité.

Exécutez la page, ajouter des informations pour un nouveau service, puis cliquez sur le **insérer** lien.

[![Image04](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image28.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image27.png)

Entrez des valeurs pour un autre service. Entrez un nombre supérieur à format 1,000,000.00 dans les **Budget** champ et onglet dans le champ suivant. Un astérisque apparaît dans le champ, et si vous maintenez le pointeur de la souris dessus, vous pouvez voir le message d’erreur que vous avez entré dans les métadonnées pour ce champ.

[![Image03](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image30.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image29.png)

Cliquez sur **insérer**, et vous voyez le message d’erreur affiché par le `ValidationSummary` contrôle en bas de la page.

[![Image12](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image32.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image31.png)

Ensuite, fermez le navigateur et ouvrez le *Departments.aspx* page. Ajouter la fonction de suppression pour le *Departments.aspx* page en ajoutant un `DeleteMethod` attribut le `ObjectDataSource` (contrôle) et un `DataKeyNames` attribut le `GridView` contrôle. Les balises d’ouverture de ces contrôles maintenant ressemblera à l’exemple suivant :

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample12.aspx)]

Exécutez la page.

[![Image09](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image34.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image33.png)

Supprimer le service que vous avez ajouté lorsque vous avez exécuté le *DepartmentsAdd.aspx* page.

## <a name="adding-update-functionality"></a>Ajout d’une fonctionnalité de mise à jour

Ouvrez *SchoolRepository.cs* et ajoutez le code suivant `Update` méthode :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample13.cs)]

Lorsque vous cliquez sur **mise à jour** dans les *Departments.aspx* page, le `ObjectDataSource` contrôle crée deux `Department` entités à passer à la `UpdateDepartment` (méthode). Un contient les valeurs d’origine qui ont été stockés dans l’état d’affichage, et l’autre les nouvelles valeurs ont été entrées dans le `GridView` contrôle. Le code dans le `UpdateDepartment` méthode passe les `Department` entité qui a les valeurs d’origine à la `Attach` méthode afin d’établir le suivi entre l’entité et ce qui figure dans la base de données. Le code transmet la `Department` entité qui possède les nouvelles valeurs à la `ApplyCurrentValues` (méthode). Le contexte de l’objet compare les valeurs anciennes et nouvelles. Si une nouvelle valeur est différente à partir d’une ancienne valeur, le contexte de l’objet change la valeur de propriété. Le `SaveChanges` méthode met à jour uniquement les colonnes modifiées dans la base de données. (Toutefois, si la fonction de mise à jour pour cette entité ont été mappée à une procédure stockée, la ligne entière sera actualisée quels que soient les colonnes ont été modifiées.)

Ouvrez le *Departments.aspx* et ajoutez les attributs suivants pour le `DepartmentsObjectDataSource` contrôle :

- `UpdateMethod="UpdateDepartment"`
- `ConflictDetection="CompareAllValues"`   
 Cette anciennes valeurs de causes pour être stocké dans la vue état afin qu’elles puissent être comparées avec les nouvelles valeurs dans le `Update` (méthode).
- `OldValuesParameterFormatString="orig{0}"`   
 Cela permet d’informer le contrôle que le nom du paramètre de valeurs d’origine est `origDepartment` .

Le balisage de la balise d’ouverture de la `ObjectDataSource` contrôle ressemble maintenant à l’exemple suivant :

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample14.aspx)]

Ajouter un `OnRowUpdating="DepartmentsGridView_RowUpdating"` attribut le `GridView` contrôle. Vous allez utiliser pour définir le `Administrator` valeur de propriété basée sur la ligne que l’utilisateur sélectionne dans une liste déroulante. Le `GridView` balise d’ouverture maintenant ressemble à l’exemple suivant :

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample15.aspx)]

Ajouter un `EditItemTemplate` de contrôler la `Administrator` colonne à la `GridView` contrôler, immédiatement après le `ItemTemplate` contrôle pour cette colonne :

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample16.aspx)]

Cela `EditItemTemplate` contrôle est semblable à la `InsertItemTemplate` contrôler dans le *DepartmentsAdd.aspx* page. La différence est que la valeur initiale du contrôle est définie à l’aide de la `SelectedValue` attribut.

Avant du `GridView` , ajoutez un `ValidationSummary` contrôle comme vous le faisiez le *DepartmentsAdd.aspx* page.

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample17.aspx)]

Ouvrez *Departments.aspx.cs* et immédiatement après la déclaration de classe partielle, ajoutez le code suivant pour créer un champ privé pour faire référence à la `DropDownList` contrôle :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample18.cs)]

Ajouter des gestionnaires pour les `DropDownList` du contrôle `Init` événement et le `GridView` du contrôle `RowUpdating` événement :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/samples/sample19.cs)]

Le gestionnaire pour le `Init` événement enregistre une référence à la `DropDownList` contrôle dans le champ de classe. Le gestionnaire pour le `RowUpdating` événement utilise la référence pour obtenir la valeur de l’utilisateur a entré et l’appliquer à la `Administrator` propriété de la `Department` entité.

Utilisez le *DepartmentsAdd.aspx* page pour ajouter un nouveau service, puis exécutez le *Departments.aspx* page, puis cliquez sur **modifier** sur la ligne que vous avez ajouté.

> [!NOTE]
> Vous ne pourrez pas modifier les lignes que vous n’avez pas ajouté (autrement dit, qui étaient déjà présents dans la base de données), en raison de données non valides dans la base de données ; les administrateurs pour les lignes qui ont été créées avec la base de données sont des étudiants. Si vous essayez de modifier un d’eux, vous obtiendrez une page d’erreur qui signale une erreur comme`'InstructorsDropDownList' has a SelectedValue which is invalid because it does not exist in the list of items.`


[![Image10](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image36.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image35.png)

Si vous entrez non valide **Budget** puis cliquez sur **mise à jour**, vous voyez l’astérisque même et le message d’erreur que vous avez vu dans les *Departments.aspx* page.

Modifier une valeur de champ ou sélectionnez un autre administrateur, puis cliquez sur **mise à jour**. La modification s’affiche.

[![Image09](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image38.png)](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started/_static/image37.png)

Cette étape termine l’introduction à l’utilisation de la `ObjectDataSource` contrôle pour CRUD de base (créer, lire, mettre à jour, supprimer) des opérations avec Entity Framework. Vous avez créé une application multicouche simple, mais la couche de logique métier est toujours étroitement à la couche d’accès aux données, ce qui complique le test unitaire automatisé. Dans ce didacticiel, vous verrez comment implémenter le modèle de référentiel pour faciliter les tests unitaires.

>[!div class="step-by-step"]
[Next](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests.md)
