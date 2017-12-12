---
uid: web-forms/overview/older-versions-getting-started/continuing-with-ef/maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application
title: Optimisation des performances avec Entity Framework 4.0 dans une Application ASP.NET 4 | Documents Microsoft
author: tdykstra
description: "Cette série de didacticiels s’appuie sur l’application web de l’université Contoso qui est créée par la prise en main de la série de didacticiels Entity Framework 4.0. JE..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/26/2011
ms.topic: article
ms.assetid: 4e43455e-dfa1-42db-83cb-c987703f04b5
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/continuing-with-ef/maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application
msc.type: authoredcontent
ms.openlocfilehash: 9e257f5061f6bdf14ad0776ff6385fb526d6dcb1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="maximizing-performance-with-the-entity-framework-40-in-an-aspnet-4-web-application"></a>Optimisation des performances avec Entity Framework 4.0 dans une Application ASP.NET 4
====================
Par [Tom Dykstra](https://github.com/tdykstra)

> Cette série de didacticiels s’appuie sur l’application web de l’université Contoso qui est créée par le [mise en route avec l’Entity Framework 4.0](https://asp.net/entity-framework/tutorials#Getting%20Started) série de didacticiels. Si vous n’avez pas terminé les didacticiels antérieures, comme point de départ pour ce didacticiel vous pouvez [télécharger l’application](https://code.msdn.microsoft.com/ASPNET-Web-Forms-97f8ee9a) que vous devriez avoir créé. Vous pouvez également [télécharger l’application](https://code.msdn.microsoft.com/ASPNET-Web-Forms-6c7197aa) qui est créé par la série de didacticiels terminée. Si vous avez des questions sur les didacticiels, vous pouvez les valider pour le [forum de ASP.NET Entity Framework](https://forums.asp.net/1227.aspx).


Dans le didacticiel précédent, vous avez vu comment gérer les conflits d’accès concurrentiel. Ce didacticiel montre les options pour améliorer les performances d’une application web ASP.NET qui utilise Entity Framework. Vous allez apprendre plusieurs méthodes pour optimiser les performances ou pour diagnostiquer des problèmes de performances.

Les informations présentées dans les sections suivantes sont susceptibles d’être utile dans un large éventail de scénarios :

- Permet de charger efficacement les données associées.
- Gérer l’état d’affichage.

Les informations présentées dans les sections suivantes peuvent être utiles si vous avez des requêtes individuelles qui présente des problèmes de performances :

- Utilisez la `NoTracking` option de fusion.
- Précompiler des requêtes LINQ.
- Examinez les commandes de requête envoyées à la base de données.

Les informations présentées dans la section suivante peut être utiles pour les applications qui ont des modèles de données extrêmement volumineux :

- Prégénérer des affichages.

> [!NOTE]
> Performances de l’application Web est affectée par de nombreux facteurs, y compris des éléments tels que la taille des données de demande et de réponse, la vitesse des requêtes de base de données, le nombre de requêtes que du serveur peut être en file et la rapidité avec laquelle il peut traiter et même l’efficacité de n’importe quel bibliothèques de scripts clients que vous utilisiez. Si les performances sont critiques dans votre application, ou si le test ou l’expérience montre que les performances de l’application n’est pas satisfaisante, vous devez suivre le protocole normal pour le réglage des performances. Mesurer pour déterminer où se produisent les goulots d’étranglement de performances et puis résolvez les domaines ayant le plus grand impact sur les performances globales de l’application.
> 
> Cette rubrique se concentre principalement sur les façons dans laquelle vous pouvez potentiellement améliorer les performances en particulier d’Entity Framework dans ASP.NET. Les suggestions ici sont utiles si vous déterminez que l’accès aux données est un des goulots d’étranglement de performances dans votre application. Mais comme indiqué, les méthodes expliquées ici ne peut être considéré comme &quot;meilleures pratiques&quot; en général, bon nombre d'entre elles conviennent uniquement dans des situations exceptionnelles ou à l’adresse très particuliers des goulots d’étranglement de performances.


Pour démarrer le didacticiel, démarrez Visual Studio et ouvrez l’application web de Contoso University que vous utilisiez dans le didacticiel précédent.

## <a name="efficiently-loading-related-data"></a>Efficacement le chargement des données associées

Il existe plusieurs façons qu’Entity Framework peut charger des données connexes dans les propriétés de navigation d’une entité :

- *Chargement différé*. Lors de la lecture de l’entité est tout d’abord, les données associées n’est pas récupérées. Toutefois, la première fois que vous tentez d’accéder à une propriété de navigation, les données requises pour cette propriété de navigation sont automatiquement récupérées. Cela entraîne plusieurs requêtes envoyées à la base de données : une pour l’entité elle-même et chaque fois que les données pour l’entité associées doivent être récupérées. 

    [![Image05](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image2.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image1.png)

*Chargement hâtif*. Lors de la lecture de l’entité, les données associées sont récupérées en même temps. Cela entraîne généralement une requête de jointure unique qui extrait toutes les données que nécessaire. Vous spécifiez un chargement hâtif à l’aide de la `Include` méthode, comme vous l’avez déjà vu dans ces didacticiels.

[![Image07](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image4.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image3.png)

- *Chargement explicite*. Cela revient à chargement différé, à ceci près que vous explicitement de récupérer les données associées dans le code ; Il ne se produit automatiquement lorsque vous accédez à une propriété de navigation. Vous chargez des données connexes manuellement à l’aide de la `Load` méthode de la propriété de navigation pour les collections, ou si vous utilisez la `Load` méthode de la propriété de référence pour les propriétés qui contiennent un seul objet. (Par exemple, vous appelez le `PersonReference.Load` méthode pour charger le `Person` propriété de navigation d’un `Department` entity.)

    [![Image06](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image6.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image5.png)

Car ils ne récupèrent pas immédiatement les valeurs de propriété, le chargement tardif et chargement explicite sont également tous deux appelés *chargement différé*.

Chargement différé est le comportement par défaut pour un contexte d’objet qui a été généré par le concepteur. Si vous ouvrez le *SchoolModel.Designer.cs* fichier qui définit la classe de contexte d’objet, vous trouverez trois méthodes de constructeur, et chacun d’eux inclut l’instruction suivante :

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample1.cs)]

En général, si vous savez que vous devez les données associées pour chaque entité récupéré, hâtif chargement offre des performances optimales, car une seule requête envoyée à la base de données est généralement plus efficace que les requêtes distinctes pour chaque entité récupérée. En revanche, si vous avez besoin accéder aux propriétés de navigation d’une entité que rarement ou uniquement pour un petit ensemble d’entités, le chargement tardif ou chargement explicite peut être plus efficace, car un chargement hâtif permet de récupérer plus de données que vous avez besoin.

Dans une application web, le chargement tardif peut être relativement peu d’intérêt, car les actions de l’utilisateur qui affectent la nécessité pour les données associées ont lieu dans le navigateur, qui dispose d’aucune connexion au contexte d’objet que la page rendue. En revanche, lorsque vous databind un contrôle, vous savez généralement les données que vous avez besoin et par conséquent, il est généralement meilleures pour choisir un chargement hâtif ou le chargement différé en fonction de ce qui est approprié dans chaque scénario.

En outre, un contrôle lié aux données peut utiliser un objet d’entité, une fois que le contexte de l’objet est supprimé. Dans ce cas, une tentative de chargement différé une propriété de navigation échouerait. Vous recevez le message d’erreur est clair :&quot;`The ObjectContext instance has been disposed and can no longer be used for operations that require a connection.`&quot;

Le `EntityDataSource` contrôle désactive le chargement différé par défaut. Pour le `ObjectDataSource` contrôle que vous utilisez pour le didacticiel actuel (ou si vous le contexte de l’objet à partir de code de la page), il existe plusieurs méthodes que vous pouvez apporter différée chargement désactivé par défaut. Vous pouvez le désactiver lorsque vous instanciez un contexte d’objet. Par exemple, vous pouvez ajouter la ligne suivante à la méthode de constructeur de la `SchoolRepository` classe :

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample2.cs)]

Pour l’application Contoso University, vous apporterez le contexte de l’objet automatiquement désactiver le chargement différé pour que cette propriété ne doit pas nécessairement être défini à chaque fois qu’un contexte est instancié.

Ouvrez le *SchoolModel.edmx* données de modèle et cliquez sur l’aire de conception, puis, dans le volet Propriétés, définissez la **activée de chargement différé** propriété `False`. Enregistrez et fermez le modèle de données.

[![Image04](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image8.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image7.png)

## <a name="managing-view-state"></a>Gestion de l’état d’affichage

Pour fournir des fonctionnalités de mise à jour, une page web ASP.NET doit stocker les valeurs d’origine de la propriété d’une entité lorsqu’une page est rendue. Lors de la publication du contrôle de traitement recréer l’état d’origine de l’entité, appelez l’entité `Attach` méthode avant d’appliquer les modifications et en appelant le `SaveChanges` (méthode). Par défaut, les contrôles de données ASP.NET Web Forms utilisent l’état d’affichage pour stocker les valeurs d’origine. Toutefois, l’état d’affichage peut affecter les performances, car il est stocké dans les champs masqués qui peuvent augmenter considérablement la taille de la page est envoyée vers et à partir du navigateur.

Techniques de gestion d’état d’affichage ou alternatives telles que l’état de session, ne sont pas propres à Entity Framework, pour ce didacticiel n’aborde ce sujet en détail. Pour plus d’informations consultez les liens à la fin du didacticiel.

Toutefois, la version 4 d’ASP.NET fournit une nouvelle façon de travailler avec l’état d’affichage que chaque développeur ASP.NET des applications Web Forms doit connaître : le `ViewStateMode` propriété. Cette nouvelle propriété peut être définie au niveau de la page ou un contrôle, et vous permet de désactiver l’état d’affichage par défaut pour une page et l’activer uniquement pour les contrôles qui en ont besoin.

Pour les applications où les performances sont critiques, une bonne pratique est toujours désactiver l’état d’affichage au niveau de la page et l’activer uniquement pour les contrôles qui en ont besoin. La taille de l’état d’affichage dans les pages de l’université Contoso ne pourraient être substantiellement réduite par cette méthode, mais pour voir comment il fonctionne, vous devez le faire le *Instructors.aspx* page. Cette page contient de nombreux contrôles, y compris un `Label` contrôle qui a désactivé l’état d’affichage. Aucun des contrôles dans cette page doit réellement être afficher l’état activé. (Le `DataKeyNames` propriété de la `GridView` contrôle spécifie l’état doit être conservée entre publications (postback), mais ces valeurs sont conservées dans l’état du contrôle, qui n’est pas affecté par le `ViewStateMode` propriété.)

Le `Page` directive et `Label` balisage contrôle actuellement ressemble à ceci :

[!code-aspx[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample3.aspx)]

Apportez les modifications suivantes :

- Ajouter `ViewStateMode="Disabled"` à le `Page` la directive.
- Supprimer `ViewStateMode="Disabled"` à partir de la `Label` contrôle.

Le balisage ressemble maintenant à l’exemple suivant :

[!code-aspx[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample4.aspx)]

État d’affichage est maintenant désactivée pour tous les contrôles. Si vous ajoutez ultérieurement à un contrôle qui n’a pas besoin d’utiliser l’état d’affichage, vous devez simplement est incluent le `ViewStateMode="Enabled"` attribut pour ce contrôle.

## <a name="using-the-notracking-merge-option"></a>À l’aide de l’Option de fusion NoTracking

Lorsqu’un contexte d’objet récupère des lignes de base de données et crée des objets d’entité qui représentent les, par défaut il suit également les objets d’entité à l’aide du Gestionnaire d’état de son objet. Ces données de suivi agit comme un cache et sont utilisées lorsque vous mettez à jour une entité. Car une application web a généralement des instances de contexte d’objet éphémère, requêtes souvent retournent des données qui ne doivent être suivis, car le contexte de l’objet qui lit les est supprimé avant d’une des entités qu'il lit servent à nouveau ou mis à jour.

Dans Entity Framework, vous pouvez spécifier si le contexte de l’objet effectue le suivi des objets d’entité en définissant un *option de fusion*. Vous pouvez définir l’option de fusion pour les requêtes individuelles ou pour les jeux d’entités. Si vous définissez pour un jeu d’entités, cela signifie que vous définissez l’option de fusion par défaut pour toutes les requêtes qui sont créés pour ce jeu d’entités.

Pour l’application Contoso University, le suivi n’est pas nécessaire pour un des jeux d’entités que vous accédez à partir du référentiel, vous pouvez définir l’option de fusion `NoTracking` pour les jeux d’entités lorsque vous instanciez le contexte de l’objet dans la classe de référentiel. (Notez que dans ce didacticiel, l’option de fusion ne sont pas avoir un effet notable sur les performances de l’application. Le `NoTracking` option est susceptible d’apporter une amélioration des performances observable dans certains scénarios de haut volume de données.)

Dans le dossier de la couche DAL, ouvrez le *SchoolRepository.cs* et ajoutez une méthode de constructeur qui définit l’option de fusion pour l’entité définit que n’accède à l’espace de stockage :

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample5.cs)]

## <a name="pre-compiling-linq-queries"></a>Compilation des requêtes LINQ

La première fois que l’Entity Framework exécute une requête Entity SQL dans la durée de vie d’une donnée `ObjectContext` instance, il prend un certain temps pour compiler la requête. Le résultat de la compilation est mis en cache, ce qui signifie que les exécutions ultérieures de la requête sont beaucoup plus rapides. Les requêtes LINQ suivent un modèle similaire, à ceci près que la partie du travail requis pour compiler la requête est effectuée chaque fois que la requête est exécutée. En d’autres termes, pour les requêtes LINQ, par défaut tous les résultats de la compilation sont mis en cache.

Si vous avez une requête LINQ susceptibles de s’exécuter à plusieurs reprises dans la vie d’un contexte d’objet, vous pouvez écrire du code qui provoque de tous les résultats de la compilation à mettre en cache lors de la première exécution de la requête LINQ.

En guise d’illustration, vous devez effectuer cette opération pour deux `Get` méthodes dans le `SchoolRepository` (classe), un n’accepte aucun paramètre (le `GetInstructorNames` (méthode)) et l’autre ne nécessitant pas de paramètre (les `GetDepartmentsByAdministrator` (méthode)). Ces méthodes comme ils se trouvent désormais réellement ne doivent être compilés, car ils ne sont pas des requêtes LINQ :

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample6.cs)]

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample7.cs)]

Toutefois, afin que vous pouvez essayer les requêtes compilées, vous devez procéder comme s’il avaient été écrit en tant que les requêtes LINQ suivantes :

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample8.cs)]

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample9.cs)]

Vous pouvez modifier le code de ces méthodes à ce qui est indiqué ci-dessus et exécutez l’application pour vérifier qu’elle fonctionne avant de continuer. Mais les instructions suivantes accéder directement à la création de versions précompilées.

Créer un fichier de classe dans le *DAL* dossier, nommez-le *SchoolEntities.cs*et remplacez le code existant par le code suivant :

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample10.cs)]

Ce code crée une classe partielle qui étend la classe de contexte d’objet généré automatiquement. La classe partielle inclut deux requêtes LINQ compilées à l’aide de la `Compile` méthode de la `CompiledQuery` classe. Il crée également des méthodes que vous pouvez utiliser pour appeler les requêtes. Enregistrez et fermez ce fichier.

Ensuite, dans *SchoolRepository.cs*, modifier existants `GetInstructorNames` et `GetDepartmentsByAdministrator` méthodes dans le référentiel de classe, afin que leur appellent les requêtes compilées :

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample11.cs)]

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample12.cs)]

Exécutez le *Departments.aspx* page pour vérifier qu’elle fonctionne comme auparavant. Le `GetInstructorNames` méthode est appelée pour remplir la liste déroulante d’administrateur et le `GetDepartmentsByAdministrator` méthode est appelée lorsque vous cliquez sur **mise à jour** afin de vérifier qu’aucun formateur n’est un administrateur de plusieurs département.

[![Image03](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image10.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image9.png)

Vous avez précompilé les requêtes dans l’application Contoso University uniquement pour apprendre à le faire, pas, car elle n’améliore sensiblement les performances. Avant la compilation de requêtes LINQ ajouter un niveau de complexité à votre code, par conséquent, assurez-vous que cela uniquement pour les requêtes qui représentent les goulots d’étranglement dans votre application.

## <a name="examining-queries-sent-to-the-database"></a>Examen des requêtes envoyées à la base de données

Lorsque vous recherchez des problèmes de performances, il est parfois utile de connaître les commandes SQL exacts qui envoie à la base de données Entity Framework. Si vous travaillez avec un `IQueryable` de l’objet, une pour ce faire consiste à utiliser le `ToTraceString` (méthode).

Dans *SchoolRepository.cs*, modifiez le code dans la `GetDepartmentsByName` méthode correspondant à l’exemple suivant :

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample13.cs)]

Le `departments` variable doit être castée en un `ObjectQuery` type uniquement, car le `Where` méthode à la fin de la ligne précédente crée un `IQueryable` de l’objet ; sans le `Where` (méthode), le cast ne serait pas nécessaire.

Définir un point d’arrêt sur la `return` de ligne, puis exécutez le *Departments.aspx* page dans le débogueur. Lorsque vous atteignez le point d’arrêt, examinez le `commandText` variable dans le **variables locales** fenêtre et utilisez le visualiseur de texte (la Loupe dans la **valeur** colonne) pour afficher sa valeur dans la **Visualiseur de texte** fenêtre. Vous pouvez voir la commande SQL entière qui résulte de ce code :

[![Image08](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image12.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image11.png)

En guise d’alternative, la fonctionnalité d’IntelliTrace dans Visual Studio Ultimate fournit un moyen d’afficher les commandes SQL générées par Entity Framework qui ne nécessite pas vous permet de modifier votre code ou de paramétrer un point d’arrêt.

> [!NOTE]
> Vous pouvez effectuer les procédures suivantes uniquement si vous avez Visual Studio Ultimate.


Restaurez le code d’origine dans le `GetDepartmentsByName` (méthode), puis exécutez le *Departments.aspx* page dans le débogueur.

Dans Visual Studio, sélectionnez le **déboguer** menu, puis **IntelliTrace**, puis **événements IntelliTrace**.

[![Image11](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image14.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image13.png)

Dans le **IntelliTrace** fenêtre, cliquez sur **interrompre tout**.

[![Image12](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image16.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image15.png)

Le **IntelliTrace** fenêtre affiche une liste d’événements récents :

[![Image09](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image18.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image17.png)

Cliquez sur le **ADO.NET** ligne. Il se développe pour afficher le texte de commande :

[![Image10](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image20.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image19.png)

Vous pouvez copier la chaîne de texte de commande entière dans le Presse-papiers à partir de la **variables locales** fenêtre.

Supposons que vous utilisiez une base de données avec plusieurs tables, relations et de colonnes que la simple `School` base de données. Vous constaterez peut-être qu’une requête qui regroupe toutes les informations que vous devez dans un seul `Select` instruction contenant plusieurs `Join` clauses devient trop complexe pour travailler efficacement. Dans ce cas, vous pouvez passer de chargement pour le chargement explicite pour simplifier la requête hâtif.

Par exemple, essayez de modifier le code dans le `GetDepartmentsByName` méthode dans *SchoolRepository.cs*. Actuellement que méthode que vous avez une requête d’objet qui a `Include` méthodes pour le `Person` et `Courses` propriétés de navigation. Remplacez le `return` instruction avec le code qui effectue un chargement explicite, comme illustré dans l’exemple suivant :

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample14.cs)]

Exécutez le *Departments.aspx* page dans le débogueur et vérifiez la **IntelliTrace** fenêtre comme vous l’avez fait précédemment. Maintenant, où il y a une seule requête avant, vous constatez une longue séquence de ceux-ci.

[![Image13](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image22.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image21.png)

Cliquez sur la première **ADO.NET** ligne pour voir ce qui est arrivé à la requête complexe affiché précédemment.

[![Image14](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image24.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image23.png)

La requête à partir des services est devenue un simple `Select` de requête sans aucune `Join` clause, mais il est suivi par des requêtes distinctes qui récupèrent des cours et un administrateur, à l’aide d’un jeu de deux requêtes pour chaque service retourné par la version d’origine requête.

> [!NOTE]
> Si vous laissez différée activé, le chargement du modèle, vous pouvez voir ici, avec la même requête répétée à plusieurs reprises, peut entraîner le chargement différé. Un modèle que vous souhaitez généralement éviter consiste à chargement différé des données associées pour chaque ligne de la table primaire. Sauf si vous avez vérifié qu’une requête de jointure unique est trop complexe pour être efficace, il se peut que vous ne pourrez généralement améliorer les performances dans de tels cas en modifiant la requête principale pour utiliser un chargement hâtif.


## <a name="pre-generating-views"></a>Prégénération d’affichages

Lorsqu’un `ObjectContext` objet est créé dans un domaine d’application, Entity Framework génère un ensemble de classes qu’il utilise pour accéder à la base de données. Ces classes sont appelées *vues*, et si vous avez un modèle de données très volumineux, génération de ces affichages permettre retarder la réponse du site web à la première requête pour une page après l’initialisation d’un domaine d’application. Vous pouvez réduire ce délai de la première requête en créant des vues au moment de la compilation plutôt qu’au moment de l’exécution.

> [!NOTE]
> Si votre application n’a pas un modèle de données extrêmement volumineux, ou si elle n’a pas de modèle de données de grande taille, mais vous ne souhaitez pas absolument à un problème de performances qui affecte uniquement la première demande de page une fois que IIS est recyclé, vous pouvez ignorer cette section. Vue de la création ne se produit chaque fois que vous instanciez une `ObjectContext` de l’objet, car les vues sont mis en cache dans le domaine d’application. Par conséquent, sauf si vous êtes souvent recyclage de votre application dans IIS, les demandes de pages très peu tireront l’à partir des vues prégénérées.


Vous pouvez prégénérer des affichages à l’aide de la *EdmGen.exe* outil de ligne de commande ou en utilisant un *Text Template Transformation Toolkit* modèle (T4). Dans ce didacticiel, vous allez utiliser un modèle T4.

Dans le *DAL* dossier, ajouter un fichier en utilisant le **modèle de texte** modèle (il se trouve sous le **général** nœud dans le **modèles installés** liste), et nommez-le *SchoolModel.Views.tt*. Remplacez le code existant dans le fichier avec le code suivant :

[!code-csharp[Main](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/samples/sample15.cs)]

Ce code génère des vues pour une *.edmx* fichier qui se trouve dans le même dossier que le modèle et qui porte le même nom que le fichier de modèle. Par exemple, si votre fichier de modèle est nommé *SchoolModel.Views.tt*, il recherche un fichier de modèle de données nommé *SchoolModel.edmx*.

Enregistrez le fichier, puis cliquez sur le fichier dans **l’Explorateur de solutions** et sélectionnez **exécuter un outil personnalisé**.

[![Image02](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image26.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image25.png)

Visual Studio génère un fichier de code qui crée les vues, qui est nommé *SchoolModel.Views.cs* basé sur le modèle. (Vous avez peut-être remarqué que le fichier de code est généré avant même que vous sélectionnez **exécuter un outil personnalisé**, dès que vous enregistrez le fichier de modèle.)

[![Image01](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image28.png)](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application/_static/image27.png)

Vous pouvez maintenant exécuter l’application et vérifier qu’elle fonctionne comme auparavant.

Pour plus d’informations sur les vues prégénérées, consultez les ressources suivantes :

- [Comment : générer des vues pour améliorer les performances de requête](https://msdn.microsoft.com/en-us/library/bb896240.aspx) sur le site web MSDN. Explique comment utiliser le `EdmGen.exe` outil de ligne de commande pour prégénérer des affichages.
- [Isolation des performances avec les vues de pré/précompilé-generated dans Entity Framework 4](https://blogs.msdn.com/b/appfabriccat/archive/2010/08/06/isolating-performance-with-precompiled-pre-generated-views-in-the-entity-framework-4.aspx) sur le blog de l’équipe de consultants de client de Windows Server AppFabric.

Cette étape termine l’introduction d’améliorer les performances dans une application web ASP.NET qui utilise Entity Framework. Pour plus d'informations, reportez-vous aux ressources suivantes :

- [Considérations sur les performances (Entity Framework)](https://msdn.microsoft.com/en-us/library/cc853327.aspx) sur le site web MSDN.
- [Les publications relatives aux performances sur le blog de l’équipe de Entity Framework](https://blogs.msdn.com/b/adonet/archive/tags/performance/).
- [EF Options de fusion et les requêtes compilées](https://blogs.msdn.com/b/dsimmons/archive/2010/01/12/ef-merge-options-and-compiled-queries.aspx). Billet de blog qui explique des comportements inattendus de requêtes compilées et de fusion options telles que `NoTracking`. Si vous envisagez d’utiliser des requêtes compilées et de manipuler les paramètres d’option de fusion dans votre application, lisez d’abord ceci.
- [Entité liée de Framework valide dans le blog de l’équipe de consultants clients de modélisation et les données](https://blogs.msdn.com/b/dmcat/archive/tags/entity+framework/). Inclut les billets sur les requêtes compilées et à l’aide du profileur 2010 de Visual Studio pour détecter les problèmes de performances.
- [Thread de forum Entity Framework avec des conseils sur l’amélioration des performances des requêtes très complexes](https://social.msdn.microsoft.com/Forums/en-US/adodotnetentityframework/thread/ffe8b2ab-c5b5-4331-8988-33a872d0b5f6).
- [Recommandations de gestion d’état ASP.NET](https://msdn.microsoft.com/en-us/library/z1hkazw7.aspx).
- [À l’aide d’Entity Framework et ObjectDataSource : la pagination personnalisée](http://geekswithblogs.net/Frez/articles/using-the-entity-framework-and-the-objectdatasource-custom-paging.aspx). Billet de blog qui s’appuie sur l’application ContosoUniversity créée dans ces didacticiels pour expliquer comment implémenter la pagination dans le *Departments.aspx* page.

Le didacticiel suivant passe en revue les améliorations importantes à Entity Framework qui sont nouvelles dans la version 4.

>[!div class="step-by-step"]
[Précédent](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application.md)
[Suivant](what-s-new-in-the-entity-framework-4.md)
