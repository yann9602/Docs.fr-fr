---
title: Pages Razor avec Entity Framework Core - didacticiel 1 de 8
author: rick-anderson
description: "Montre comment créer une application de Pages Razor à l’aide d’Entity Framework Core"
keywords: Didacticiel de ASP.NET Core, Entity Framework Core,
ms.author: riande
manager: wpickett
ms.date: 11/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/intro
ms.openlocfilehash: 86f9eceb5b8646e371811fa4611a4509ff652231
ms.sourcegitcommit: 2d23ea501e0213bbacf65298acf1c8bd17209540
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="getting-started-with-razor-pages-and-entity-framework-core-using-visual-studio-1-of-8"></a>Prise en main de Pages Razor et Entity Framework Core, à l’aide de Visual Studio (1 / 8)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

L’exemple d’application web de Contoso University montre comment créer des applications web ASP.NET Core 2.0 MVC à l’aide d’Entity Framework (EF) 2.0 et Visual Studio 2017.

L’exemple d’application est un site web pour une université fictif de Contoso. Il inclut des fonctionnalités telles que leur admission d’étudiant, la création de cours et les affectations de formateur. Cette page est la première d’une série de didacticiels qui expliquent comment générer l’exemple d’application Contoso University.

[Télécharger ou afficher l’application terminée.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Les instructions de téléchargement](xref:tutorials/index#how-to-download-a-sample).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

Vous êtes familiarisé avec [Pages Razor](xref:mvc/razor-pages/index). Les programmeurs doivent terminer [prise en main Pages Razor](xref:tutorials/razor-pages/razor-pages-start) avant de démarrer cette série.

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous rencontrez un problème que vous ne pouvez pas résoudre, vous trouverez généralement la solution en comparant votre code pour le [terminé la phase](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots) ou [projet achevé](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu-final). Pour obtenir la liste des erreurs courantes et comment les résoudre, consultez [la section de dépannage du didacticiel dernière dans la série](xref:data/ef-mvc/advanced#common-errors). Si vous ne trouvez pas ce dont vous avez besoin il, vous pouvez publier une question dans StackOverflow.com pour [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Cette série de didacticiels s’appuie sur l’action effectuée dans les didacticiels antérieures. Pensez à enregistrer une copie du projet après chaque didacticiel réussite. Si vous rencontrez des problèmes, vous pouvez recommencer à partir du didacticiel précédent, au lieu de revenir au début. Vous pouvez également télécharger un [terminé la phase](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots) et tout recommencer à l’aide de l’étape terminée.

## <a name="the-contoso-university-web-app"></a>L’application web de Contoso University

L’application générée dans ces didacticiels est un site web de base university.

Les utilisateurs peuvent afficher et mettre à jour des étudiants, les cours et les informations de formateur. Voici quelques exemples d’écrans créés dans le didacticiel.

![Page d’Index les étudiants](intro/_static/students-index.png)

![Page de modification des étudiants](intro/_static/student-edit.png)

Le style de l’interface utilisateur de ce site est proche de ce qui est généré par les modèles prédéfinis. Le didacticiel focus est sur Core EF comportant des Pages Razor, pas l’interface utilisateur.

## <a name="create-a-razor-pages-web-app"></a>Créer une application de web Pages Razor

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.
* Créez une application web ASP.NET Core. Nommez le projet **ContosoUniversity**. Il est important de nommer le projet *ContosoUniversity* afin que les espaces de noms corresponde à lorsque le code est copiées/collées.
 ![Nouvelle application web ASP.NET Core](intro/_static/np.png)
* Sélectionnez **ASP.NET Core 2.0** dans la liste déroulante, puis sélectionnez **Application web**.
 ![Application web (pages Razor)](../../mvc/razor-pages/index/_static/np2.png)

Appuyez sur **F5** pour exécuter l’application en mode débogage ou sur **Ctrl+F5** pour l’exécuter sans attachement du débogueur

## <a name="set-up-the-site-style"></a>Définir le style de site

Quelques modifications définissent le menu du site, la disposition et la page d’accueil.

Ouvrez *Pages/_Layout.cshtml* et apportez les modifications suivantes :

* Remplacer chaque occurrence de « ContosoUniversity » par « Contoso université. » Il existe trois occurrences.

* Ajouter des entrées de menu pour **étudiants**, **cours**, **instructeurs**, et **départements**et supprimer la **Contact** entrée de menu.

Les modifications sont mises en surbrillance. (Tout le balisage est *pas* affichés.)

[!code-html[](intro/samples/cu/Pages/_Layout.cshtml?highlight=6,29,35-38,47&range=1-50)]

Dans *Pages/Index.cshtml*, remplacez le contenu du fichier par le code suivant pour remplacer le texte sur ASP.NET et MVC avec du texte sur cette application :

[!code-html[](intro/samples/cu/Pages/Index.cshtml)]

Appuyez sur CTRL+F5 pour exécuter le projet. La page d’accueil s’affiche avec les onglets créés dans les didacticiels suivants :

![Page d’accueil de l’université Contoso](intro/_static/home-page.png)

## <a name="create-the-data-model"></a>Créer le modèle de données

Créer des classes d’entité pour l’application Contoso University. Démarrer avec les trois entités suivantes :

![Diagramme de modèle de données de l’étudiant de l’inscription de cours](intro/_static/data-model-diagram.png)

Il existe une relation un-à-plusieurs entre `Student` et `Enrollment` entités. Il existe une relation un-à-plusieurs entre `Course` et `Enrollment` entités. Un étudiant peut inscrire dans n’importe quel nombre de cours. Un cours peut avoir n’importe quel nombre d’élèves inscrits.

Dans les sections suivantes, une classe pour chacune de ces entités est créée.

### <a name="the-student-entity"></a>L’entité Student

![Diagramme de l’entité étudiant](intro/_static/student-entity.png)

Créer un *modèles* dossier. Dans le *modèles* dossier, créez un fichier de classe nommé *Student.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

Le `ID` propriété devient la colonne de clé primaire de la table de base de données (DB) qui correspond à cette classe. Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire.

Le `Enrollments` est une propriété de navigation. Propriétés de navigation se lier à d’autres entités qui sont associées à cette entité. Dans ce cas, le `Enrollments` propriété d’un `Student entity` contient toutes les `Enrollment` entités qui sont associées à cette `Student`. Par exemple, si une ligne de l’étudiant dans la base de données sont deux en relation avec l’inscription, le `Enrollments` propriété de navigation contient ces deux `Enrollment` entités. Un `Enrollment` ligne est une ligne qui contient la valeur de clé primaire qui student dans les `StudentID` colonne. Par exemple, supposons que le stagiaire avec l’ID = 1 a deux lignes la `Enrollment` table. Le `Enrollment` table comporte deux lignes avec `StudentID` = 1. `StudentID`est une clé étrangère dans la `Enrollment` table qui spécifie l’étudiant dans la `Student` table.

Si une propriété de navigation peut contenir plusieurs entités, la propriété de navigation doit être du type de la liste, tel que `ICollection<T>`. `ICollection<T>`peut être spécifié, ou un type tel que `List<T>` ou `HashSet<T>`. Lorsque `ICollection<T>` est utilisé, de EF Core crée un `HashSet<T>` collection par défaut. Les propriétés de navigation qui contiennent plusieurs entités proviennent des relations plusieurs-à-plusieurs et un-à-plusieurs.

### <a name="the-enrollment-entity"></a>L’entité de l’inscription

![Diagramme de l’entité d’inscription](intro/_static/enrollment-entity.png)

Dans le *modèles* dossier, créez *Enrollment.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

Le `EnrollmentID` propriété est la clé primaire. Cette entité utilise le `classnameID` de modèle au lieu de `ID` comme le `Student` entité. En général, les développeurs choisir un modèle et l’utiliser dans tout le modèle de données. Dans un prochain didacticiel à l’aide de code sans classname est indiqué pour le rendre plus facile à implémenter l’héritage dans le modèle de données.

Le `Grade` propriété est un `enum`. Le point d’interrogation après la `Grade` déclaration de type indique que le `Grade` propriété est nullable. Un niveau qui a la valeur null est différent de zéro une note : null signifie une note n’est pas connu ou n’a pas encore été affectée.

Le `StudentID` propriété est une clé étrangère, et la propriété de navigation correspondante est `Student`. Un `Enrollment` entité est associée à un `Student` entité, par conséquent, la propriété contienne un seul `Student` entité. Le `Student` entité diffère de la `Student.Enrollments` propriété de navigation, qui contient plusieurs `Enrollment` entités.

Le `CourseID` propriété est une clé étrangère, et la propriété de navigation correspondante est `Course`. Un `Enrollment` entité est associée à un `Course` entité.

EF Core interprète une propriété comme une clé étrangère s’il est nommé `<navigation property name><primary key property name>`. Par exemple,`StudentID` pour le `Student` propriété de navigation, dans la mesure où le `Student` la clé primaire de l’entité est `ID`. Propriétés de clé étrangère peuvent également être nommées `<primary key property name>`. Par exemple, `CourseID` depuis le `Course` la clé primaire de l’entité est `CourseID`.

### <a name="the-course-entity"></a>L’entité de cours

![Diagramme d’entité de cours](intro/_static/course-entity.png)

Dans le *modèles* dossier, créez *Course.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

Le `Enrollments` est une propriété de navigation. A `Course` entité peut être associée à un nombre quelconque de `Enrollment` entités.

Le `DatabaseGenerated` attribut permet à l’application spécifier la clé primaire plutôt que d’avoir de la base de données de sa génération.

## <a name="create-the-schoolcontext-db-context"></a>Créer le contexte de base de données SchoolContext

La classe principale qui coordonne les fonctionnalités principales d’EF pour un modèle de données spécifiée est la classe de contexte de base de données. Le contexte de données est dérivé de `Microsoft.EntityFrameworkCore.DbContext`. Le contexte de données spécifie les entités qui sont incluses dans le modèle de données. Dans ce projet, la classe est nommée `SchoolContext`.

Dans le dossier du projet, créez un dossier nommé *données*.

Dans le *données* créer de dossier *SchoolContext.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Ce code crée un `DbSet` propriété pour chaque jeu d’entités. Dans la terminologie d’EF Core :

* En général, une entité correspond à une table de base de données.
* Une entité correspond à une ligne dans la table.

`DbSet<Enrollment>`et `DbSet<Course>` peut être omis. EF Core inclut les implicitement, car le `Student` références d’entité le `Enrollment` entité et le `Enrollment` références d’entité le `Course` entité. Pour ce didacticiel, conservez `DbSet<Enrollment>` et `DbSet<Course>` dans le `SchoolContext`.

Lorsque la base de données est créé, EF Core crée des tables qui ont des noms identique à la `DbSet` les noms de propriété. Les noms de propriété pour les collections sont en général au pluriel (étudiants plutôt qu’étudiant). Les développeurs sont en désaccord sur si les noms de table doivent être au pluriel. Pour ces didacticiels, le comportement par défaut est substitué en spécifiant des noms de table unique dans le DbContext. Pour spécifier les noms de table au singulier, ajoutez le code en surbrillance suivant :

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-context-with-dependency-injection"></a>Enregistrer le contexte avec l’injection de dépendance

ASP.NET Core inclut [injection de dépendance](xref:fundamentals/dependency-injection). Services (par exemple, le contexte de la base de données EF Core) sont enregistrés avec l’injection de dépendance au cours du démarrage de l’application. Composants qui requièrent ces services (tels que les Pages Razor) sont fournis à ces services via les paramètres du constructeur. Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le didacticiel.

Pour inscrire `SchoolContext` en tant que service, ouvrez *Startup.cs*et ajoutez les lignes en surbrillance vers le `ConfigureServices` (méthode).

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

Le nom de la chaîne de connexion est passé dans le contexte en appelant une méthode sur un `DbContextOptionsBuilder` objet. Pour le développement local, le [système de configuration ASP.NET Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir de la *appsettings.json* fichier.

Ajouter `using` instructions pour `ContosoUniversity.Data` et `Microsoft.EntityFrameworkCore` espaces de noms. Générez le projet.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Ouvrez le *appsettings.json* et ajoutez une chaîne de connexion comme indiqué dans le code suivant :

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

La chaîne de connexion précédente utilise `ConnectRetryCount=0` pour empêcher [SQLClient](https://docs.microsoft.com/dotnet/framework/data/adonet/ef/sqlclient-for-the-entity-framework) blocage.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

La chaîne de connexion spécifie une base de données SQL Server LocalDB. LocalDB est une version légère de SQL Server Express Database Engine et est prévu pour le développement d’applications, pas les fins de production. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, LocalDB crée *.mdf* les fichiers de base de données dans le `C:/Users/<user>` active.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Ajoutez du code pour initialiser la base de données avec des données de test

EF Core crée une base de données vide. Dans cette section, un *Seed* méthode est écrite à remplir avec des données de test.

Dans le *données* dossier, créez un nouveau fichier de classe nommé *DbInitializer.cs* et ajoutez le code suivant :

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Le code vérifie s’il existe des étudiants dans la base de données. S’il n’y a aucun élève dans la base de données, la base de données est amorcé avec les données de test. Il charge les données de test dans les tableaux plutôt que `List<T>` collections pour optimiser les performances.

Le `EnsureCreated` méthode crée automatiquement la base de données pour le contexte de base de données. Si la base de données existe, `EnsureCreated` retourne sans modifier la base de données.

Dans *Program.cs*, modifiez le `Main` méthode pour effectuer les opérations suivantes :

* Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendance.
* Appelez la méthode de valeur initiale, en lui passant le contexte.
* Supprimer le contexte lorsque la méthode de la valeur initiale est terminée.

Le code suivant illustre la mise à jour *Program.cs* fichier.

[!code-csharp[Main](intro/samples/cu/ProgramOriginal.cs?name=snippet)]

La première fois que l’application est exécutée, la base de données est créée et amorcée avec les données de test. Lorsque le modèle de données est mise à jour :
* Supprimer la base de données.
* Mettre à jour la méthode de valeur initiale.
* Exécuter l’application et une nouvelle base de données amorcée est créé. 

Dans les didacticiels suivants, la base de données est mise à jour lorsque le modèle de données change, sans supprimer et recréer la base de données.

<a name="pmc"></a>
## <a name="add-scaffold-tooling"></a>Ajouter des outils de la vue de structure

Dans cette section, la Console Gestionnaire de Package (PMC) est utilisé pour ajouter le package de la génération de code web Visual Studio. Ce package est nécessaire à l’exécution du moteur de génération de modèles automatique.

Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package**.

Dans Package Manager Console (PMC), entrez les commandes suivantes :

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Utils
```

La commande précédente ajoute les packages NuGet dans le fichier *.csproj :

[!code-csharp[Main](intro/samples/cu/ContosoUniversity1_csproj.txt?highlight=7-8)]

<a name="scaffold"></a>
## <a name="scaffold-the-model"></a>Structure du modèle

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).
* Exécutez les commandes suivantes :


 ```console
dotnet restore
dotnet aspnet-codegenerator razorpage -m Student -dc SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
 ```
 
Si l’erreur suivante est générée :

```text
Unhandled Exception: System.IO.FileNotFoundException: 
Could not load file or assembly 
'Microsoft.VisualStudio.Web.CodeGeneration.Utils, 
Version=2.0.0.0, Culture=neutral, PublicKeyToken=adb9793829ddae60'.
The system cannot find the file specified.
```

Exécutez de nouveau la commande et laisser un commentaire au bas de la page.

Si vous obtenez l’erreur :
  ```
No executable found matching command "dotnet-aspnet-codegenerator"
  ```

Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).


Générez le projet. La build génère des erreurs comme suit :

 `1>Pages\Students\Index.cshtml.cs(26,38,26,45): error CS1061: 'SchoolContext' does not contain a definition for 'Student'`

 Modifier globalement `_context.Student` à `_context.Students` (autrement dit, ajoutez un « s » pour `Student`). 7 occurrences sont trouvées et mis à jour. Nous espérons résoudre [ce bogue](https://github.com/aspnet/Scaffolding/issues/633) dans la prochaine version.

[!INCLUDE[model4tbl](../../includes/RP/model4tbl.md)]

 <a name="test"></a>
### <a name="test-the-app"></a>Tester l’application

Exécutez l’application et sélectionnez le **étudiants** lien. En fonction de la largeur du navigateur, le **étudiants** lien s’affiche en haut de la page. Si le **étudiants** lien n’est pas visible, cliquez sur l’icône de navigation dans le coin supérieur droit.

![Page d’accueil Contoso University étroit](intro/_static/home-page-narrow.png)

Test du **créer**, **modifier**, et **détails** des liens.

## <a name="view-the-db"></a>Afficher la base de données

Lorsque l’application est lancée, `DbInitializer.Initialize` appelle `EnsureCreated`. `EnsureCreated`détecte si la base de données existe et qu’il crée un si nécessaire. S’il n’y aucun élève dans la base de données, le `Initialize` méthode ajoute les étudiants.

Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir de la **vue** menu dans Visual Studio.
Dans SSOX, cliquez sur **(localdb) \MSSQLLocalDB > bases de données > ContosoUniversity1**.

Développez le **Tables** nœud.

Avec le bouton droit le **Student** de table et cliquez sur **des données d’affichage** pour afficher les colonnes créées et les lignes insérées dans la table.

Le *.mdf* et *.ldf* les fichiers de base de données se trouvent dans le *C:\Users\\ <yourusername>*  dossier.

`EnsureCreated`est appelé sur le démarrage de l’application, ce qui permet le flux de travail suivant :

* Supprimer la base de données.
* Modifiez le schéma de base de données (par exemple, ajouter un `EmailAddress` champ).
* Exécutez l’application.

`EnsureCreated`Crée une base de données avec la`EmailAddress` colonne.

## <a name="conventions"></a>Conventions

La quantité de code écrit dans l’ordre pour les principaux EF créer une base de données complète est minime en raison de l’utilisation des conventions ou les hypothèses qu’EF Core.

* Les noms des `DbSet` propriétés sont utilisées comme noms de tables. Pour les entités non référencées par un `DbSet` propriété, classe d’entité noms sont utilisés comme noms de tables.

* Noms de propriété d’entité sont utilisées pour les noms de colonne.

* Propriétés de l’entité qui sont nommées ID ou classnameID sont reconnues comme propriétés de clé primaire.

* Une propriété est interprétée comme une propriété de clé étrangère s’il est nommé  *<navigation property name> <primary key property name>*  (par exemple, `StudentID` pour le `Student` propriété de navigation depuis la `Student` la clé primaire de l’entité est `ID`). Propriétés de clé étrangère peuvent être nommées  *<primary key property name>*  (par exemple, `EnrollmentID` depuis le `Enrollment` la clé primaire de l’entité est `EnrollmentID`).

Un comportement conventionnel peut être remplacé. Par exemple, les noms de table peuvent être explicitement spécifiés, comme indiqué précédemment dans ce didacticiel. Les noms de colonne peuvent être définis explicitement. Clés primaires et étrangères peut être défini explicitement.

## <a name="asynchronous-code"></a>Code asynchrone

La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.

Un serveur web a un nombre limité de threads disponibles, et dans les situations de forte charge tous les threads disponibles peuvent être en cours d’utilisation. Lorsque cela se produit, le serveur ne peut pas traiter de nouvelles demandes jusqu'à ce que les threads ne sont pas libérées. Avec le code synchrone, plusieurs threads peuvent être bloqués dans pendant qu’ils ne sont pas réellement effectuer aucun travail car ils sont en attente pour les e/s. Avec le code asynchrone, lorsqu’un processus est en attente d’e/s, son thread est libéré pour le serveur à utiliser pour traiter d’autres demandes. Par conséquent, code asynchrone permet d’utiliser plus efficacement les ressources serveur et le serveur est activé pour gérer plus de trafic sans délai.

Code asynchrone introduit une petite quantité de charge au moment de l’exécution. Dans les situations de faible trafic, le gain de performances est négligeable, lors de la pour les cas de trafic élevé, l’amélioration potentielle des performances est importante.

Dans le code suivant, le `async` (mot clé), `Task<T>` valeur de retour, `await` (mot clé), et `ToListAsync` méthode rendre le code à exécuter de façon asynchrone.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Le `async` mot clé indique au compilateur de :

  * Génèrent des rappels pour les parties du corps de méthode.
  * Créer automatiquement le [tâche](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task?view=netframework-4.7) objet qui est retourné. Pour plus d’informations, consultez [Type de retour de tâche](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Type de retour implicite `Task` représente le travail en cours.

* Le `await` (mot clé), le compilateur fractionner la méthode en deux parties. La première partie se termine par l’opération qui est démarrée en mode asynchrone. La deuxième partie est placée dans une méthode de rappel qui est appelée lorsque l’opération se termine.

* `ToListAsync`est la version asynchrone de la `ToList` méthode d’extension.

Éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :

* Uniquement les instructions qui génèrent des requêtes ou des commandes à envoyer à la base de données sont exécutées de façon asynchrone. Qui inclut, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, et `SaveChangesAsync`. Il n’inclut pas d’instructions qui modifient simplement un `IQueryable`, tel que `var students = context.Students.Where(s => s.LastName == "Davolio")`.

* Un contexte EF n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle. 

* Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (tels que la pagination) utilisent async si celles-ci appellent des méthodes EF Core qui envoient des requêtes à la base de données.

Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [vue d’ensemble de Async](https://docs.microsoft.com/dotnet/articles/standard/async).

Dans le didacticiel suivant, CRUD de base (créer, lire, mettre à jour, supprimer) les opérations.

>[!div class="step-by-step"]
[Next](xref:data/ef-rp/crud)
