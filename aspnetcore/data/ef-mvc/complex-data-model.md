---
title: "Cœur de ASP.NET MVC avec EF Core - modèle de données - 5, 10"
author: tdykstra
description: "Dans ce didacticiel, vous ajoutez des entités et relations et personnalisez le modèle de données en spécifiant la mise en forme, la validation et les règles de mappage de base de données."
keywords: "Annotations de données ASP.NET Core, Entity Framework Core,"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 0dd63913-a041-48b6-96a4-3aeaedbdf5d0
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: dde50f766dc9842089cbb0561b8bd6e2d8e7c34f
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2017
---
# <a name="creating-a-complex-data-model---ef-core-with-aspnet-core-mvc-tutorial-5-of-10"></a>Création d’un modèle de données complexes - Core EF avec le didacticiel d’ASP.NET MVC de base (5 sur 10)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET MVC de base à l’aide d’Entity Framework Core et Visual Studio. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).

Dans les didacticiels précédents, vous avez travaillé avec un modèle de données simple composé de trois entités. Dans ce didacticiel, vous allez ajouter des entités et relations, et vous allez personnaliser le modèle de données en spécifiant la mise en forme, la validation et les règles de mappage de base de données.

Lorsque vous avez terminé, les classes d’entité composent le modèle de données qui est indiqué dans l’illustration suivante :

![Diagramme d’entité](complex-data-model/_static/diagram.png)

## <a name="customize-the-data-model-by-using-attributes"></a>Personnaliser le modèle de données à l’aide d’attributs

Dans cette section, vous allez apprendre à personnaliser le modèle de données à l’aide des attributs qui spécifient la mise en forme, validation et les règles de mappage de base de données. Puis dans plusieurs des sections suivantes, vous allez créer le modèle de données School complète en ajoutant des attributs aux classes que vous avez déjà créé et créer de nouvelles classes pour les autres types d’entités dans le modèle.

### <a name="the-datatype-attribute"></a>L’attribut de type de données

Pour les dates d’inscription étudiant, toutes les pages web actuellement affichent l’heure, ainsi que la date, même si tout vous intéressent pour ce champ est la date. À l’aide des attributs d’annotations de données, vous pouvez apporter une modification qui permet de corriger le format d’affichage dans chaque vue qui affiche les données de code. Pour voir un exemple de procédure, vous allez ajouter un attribut à la `EnrollmentDate` propriété dans la `Student` classe.

Dans *Models/Student.cs*, ajouter un `using` instruction pour le `System.ComponentModel.DataAnnotations` espace de noms et ajoutez `DataType` et `DisplayFormat` des attributs à la `EnrollmentDate` la propriété, comme indiqué dans l’exemple suivant :

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

L’attribut `DataType` sert à spécifier un type de données qui est plus spécifique que le type intrinsèque de la base de données. Dans ce cas, nous voulons uniquement le suivi de la date, pas la date et l’heure. Le `DataType` énumération fournit de nombreux types de données, telles que Date, Time, numéro de téléphone, devise, EmailAddress et bien plus encore. L’attribut `DataType` peut également permettre à l’application de fournir automatiquement des fonctionnalités propres au type. Par exemple, vous pouvez créer un lien `mailto:` pour `DataType.EmailAddress`, et vous pouvez fournir un sélecteur de date pour `DataType.Date` dans les navigateurs qui prennent en charge HTML5. Le `DataType` émet un attribut HTML 5 `data-` attributs (données prononcé tiret) qui peuvent de comprendre les navigateurs HTML 5. Le `DataType` attributs ne fournissent pas de validation.

`DataType.Date` ne spécifie pas le format de la date qui s’affiche. Par défaut, le champ de données s’affiche selon les formats par défaut en fonction CultureInfo du serveur.

L’attribut `DisplayFormat` est utilisé pour spécifier explicitement le format de date :

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Le paramètre `ApplyFormatInEditMode` indique que la mise en forme doit également être appliquée quand la valeur est affichée dans une zone de texte à des fins de modification. (Vous pouvez que pour certains champs--par exemple, pour les valeurs de devise, vous pouvez le symbole de devise dans la zone de texte pour le modifier.)

Vous pouvez utiliser la `DisplayFormat` attribut par lui-même, mais il est généralement judicieux d’utiliser le `DataType` également d’attribut. Le `DataType` attribut donne la sémantique des données par opposition à comment effectuer le rendu sur un écran et qu’il offre les avantages suivants que vous n’obtenez pas avec `DisplayFormat`:

* Le navigateur peut activer des fonctionnalités HTML5 (par exemple pour afficher un contrôle de calendrier, le symbole monétaire approprié de paramètres régionaux, les liens de courrier électronique, certains côté client d’entrée validation, etc..).

* Par défaut, le navigateur affiche les données à l’aide du format correspondant à vos paramètres régionaux.

Pour plus d’informations, consultez la [ \<d’entrée > documentation de l’application d’assistance de balise](../../mvc/views/working-with-forms.md#the-input-tag-helper).

Exécuter l’application, accédez à la page d’Index des étudiants et notez que fois ne sont plus affichés pour les dates d’inscription. Le même aura la valeur true pour n’importe quelle vue qui utilise le modèle de Student.

![Affichage des dates sans heures dans la page d’index les étudiants](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>L’attribut StringLength

Vous pouvez également spécifier des règles de validation de données et les messages d’erreur de validation à l’aide d’attributs. Le `StringLength` attribut définit la longueur maximale de la base de données et fournit des côté client et côté serveur validation pour ASP.NET MVC. Vous pouvez également spécifier la longueur de chaîne minimale dans cet attribut, mais la valeur minimale n’a aucun impact sur le schéma de base de données.

Supposons que vous souhaitez vous assurer que les utilisateurs n’entrent pas plus de 50 caractères pour un nom. Pour ajouter cette limitation, ajoutez `StringLength` des attributs à la `LastName` et `FirstMidName` propriétés, comme indiqué dans l’exemple suivant :

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Le `StringLength` attribut ne sera pas empêcher un utilisateur d’entrer un espace blanc pour un nom. Vous pouvez utiliser la `RegularExpression` attribut à appliquer des restrictions à l’entrée. Par exemple, le code suivant requiert le premier caractère en majuscules et les autres caractères alphabétique :

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z''-'\s]*$")]
```

Le `MaxLength` attribut fournit des fonctionnalités similaires à la `StringLength` d’attribut, mais ne fournit pas côté client validation.

Le modèle de base de données a maintenant changé d’une manière qui nécessite une modification dans le schéma de base de données. Migrations vous permet de mettre à jour le schéma sans perdre de données que vous avez peut-être ajoutés à la base de données à l’aide de l’interface utilisateur de l’application.

Enregistrez vos modifications et générez le projet. Ouvrez la fenêtre de commande dans le dossier du projet, puis entrez les commandes suivantes :

```console
dotnet ef migrations add MaxLengthOnNames
```

```console
dotnet ef database update
```

Le `migrations add` commande vous avertit qu’une perte de données peut-être se produire, car la modification la plus courte pour les deux colonnes de longueur maximale.  Migrations crée un fichier nommé * \<timeStamp > _MaxLengthOnNames.cs*. Ce fichier contient le code dans la `Up` méthode qui met à jour la base de données correspond au modèle de données en cours. Le `database update` commande exécution de ce code.

L’horodateur le préfixe au nom de fichier migrations est utilisée par Entity Framework pour classer les migrations. Vous pouvez créer plusieurs migrations avant d’exécuter la commande de base de données de mise à jour, puis toutes les migrations sont appliquées dans l’ordre dans lequel ils ont été créés.

Exécuter l’application, sélectionnez le **étudiants** , cliquez sur **créer un nouveau**et entrez le nom de plus de 50 caractères. Lorsque vous cliquez sur **créer**, validation côté client affiche un message d’erreur.

![Page affichant les erreurs de longueur de chaîne d’index les étudiants](complex-data-model/_static/string-length-errors.png)

### <a name="the-column-attribute"></a>L’attribut de colonne

Vous pouvez également utiliser des attributs pour contrôler la façon dont les classes et les propriétés sont mappées à la base de données. Supposons que vous aviez utilisé le nom `FirstMidName` pour le prénom, car le champ peut également contenir un deuxième prénom. Mais vous souhaitez que la colonne de base de données nommé `FirstName`, car les utilisateurs qui doit écrire des requêtes ad-hoc par rapport à la base de données sont habitués à ce nom. Pour effectuer ce mappage, vous pouvez utiliser la `Column` attribut.

Le `Column` attribut spécifie que lorsque la base de données est créé, la colonne de la `Student` table qui mappe à la `FirstMidName` propriété sera nommée `FirstName`. En d’autres termes, lorsque votre code fait référence à `Student.FirstMidName`, les données proviennent ou être mis à jour dans le `FirstName` colonne de la `Student` table. Si vous ne spécifiez pas les noms de colonnes, elles reçoivent le même nom que le nom de propriété.

Dans le *Student.cs* , ajoutez un `using` instruction pour `System.ComponentModel.DataAnnotations.Schema` et ajoutez l’attribut de nom de colonne à la `FirstMidName` la propriété, comme indiqué dans le code en surbrillance suivant :

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

L’ajout de la `Column` attribut modifie la sauvegarde du modèle le `SchoolContext`, donc il ne correspond pas à la base de données.

Enregistrez vos modifications et générez le projet. Ouvrez la fenêtre de commande dans le dossier du projet, puis entrez les commandes suivantes pour créer une autre migration :

```console
dotnet ef migrations add ColumnFirstName
```

```console
dotnet ef database update
```

Dans **l’Explorateur d’objets SQL Server**, ouvrez le Concepteur de tables Student en double-cliquant sur le **étudiant** table.

![Table d’étudiants dans SSOX après la migration](complex-data-model/_static/ssox-after-migration.png)

Avant d’appliquer les deux premières migrations, les colonnes de nom sont de type nvarchar (max). Il s’agit de nvarchar (50) et le nom de colonne est devenue FirstMidName FirstName.

> [!Note]
> Si vous essayez de compiler avant de terminer la création de toutes les classes d’entité dans les sections suivantes, vous pouvez obtenir les erreurs du compilateur.

## <a name="final-changes-to-the-student-entity"></a>Modifications finales à l’entité Student

![Entité Student](complex-data-model/_static/student-entity.png)

Dans *Models/Student.cs*, remplacez le code que vous avez ajouté précédemment par le code suivant. Les modifications sont mises en surbrillance.

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>L’attribut requis

Le `Required` attribut rend les champs obligatoires de propriétés de nom. Le `Required` attribut n’est pas nécessaire pour les types non nullable tels que des types valeur (DateTime, int, double, float, etc..). Les types qui ne peut pas être null sont traitées automatiquement en tant que champs requis.

Vous pouvez supprimer la `Required` d’attribut et les remplacer par un paramètre de longueur minimale pour le `StringLength` attribut :

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>L’attribut d’affichage

Le `Display` attribut spécifie que la légende pour les zones de texte doit être « Prénom », « Last Name », « Nom complet » et « Date d’inscription », au lieu du nom de propriété dans chaque instance (qui n’a pas les mots de la division d’espace).

### <a name="the-fullname-calculated-property"></a>La propriété FullName calculée

`FullName`est une propriété calculée qui retourne une valeur qui est obtenue par concaténation de deux autres propriétés. Par conséquent, il a uniquement un accesseur get et non `FullName` colonne sera générée dans la base de données.

## <a name="create-the-instructor-entity"></a>Créer l’entité Instructor

![Entité Instructor](complex-data-model/_static/instructor-entity.png)

Créer *Models/Instructor.cs*, en remplaçant le code du modèle avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Notez que plusieurs propriétés sont identiques dans les entités Student et formateur. Dans le [implémentation de l’héritage](inheritance.md) didacticiel plus loin dans cette série, vous devez refactoriser ce code pour éliminer la redondance.

Vous pouvez placer plusieurs attributs sur une seule ligne, afin que vous pouvez également écrire le `HireDate` attributs comme suit :

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Les propriétés de navigation CourseAssignments et OfficeAssignment

Le `CourseAssignments` et `OfficeAssignment` propriétés sont des propriétés de navigation.

Formateur pouvez animer n’importe quel nombre de cours, de sorte que `CourseAssignments` est défini comme une collection.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Si une propriété de navigation peut contenir plusieurs entités, son type doit être une liste dans laquelle les entrées peuvent être ajoutées, supprimées et mis à jour.  Vous pouvez spécifier `ICollection<T>` ou un type tel que `List<T>` ou `HashSet<T>`. Si vous spécifiez `ICollection<T>`, EF crée un `HashSet<T>` collection par défaut.

La raison pour laquelle ils sont `CourseAssignment` entités sont expliquées ci-dessous dans la section sur les relations plusieurs-à-plusieurs.

L’état des règles d’entreprise Contoso University qu’un formateur peut avoir uniquement au plus un bureau, donc la `OfficeAssignment` propriété contient une seule entité OfficeAssignment (qui peut être null si aucune office n’est affectée).

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>Créer l’entité OfficeAssignment

![Entité OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Créer *Models/OfficeAssignment.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>L’attribut de clé

Il existe une relation un-à-zéro-ou-un entre le formateur et les entités OfficeAssignment. Une attribution existe uniquement en relation avec le formateur, à qu'il est assigné, et par conséquent sa clé primaire est également sa clé étrangère vers l’entité Instructor. Mais Entity Framework ne peut pas reconnaître automatiquement InstructorID comme clé primaire de cette entité, car son nom ne suit pas la convention d’affectation de noms ID ou classnameID. Par conséquent, le `Key` attribut est utilisé pour identifier en tant que la clé :

```csharp
[Key]
public int InstructorID { get; set; }
```

Vous pouvez également utiliser le `Key` si l’entité n’a pas sa propre clé primaire, mais que vous souhaitez la propriété donner un nom autre que classnameID ou ID d’attribut

Par défaut, EF traite la touche non généré à la base de données, car la colonne est pour une relation d’identification.

### <a name="the-instructor-navigation-property"></a>La propriété de navigation de formateur

Entité Instructor a nullable `OfficeAssignment` propriété de navigation (parce qu’un formateur n’est peut-être pas une assignation office), et l’entité OfficeAssignment a non-nullable `Instructor` propriété de navigation (car une attribution ne peut pas exister sans un formateur-- `InstructorID` non-null). Lorsqu’une entité Instructor possède une entité OfficeAssignment associée, chaque entité a une référence à l’autre dans sa propriété de navigation.

Vous pouvez placer un `[Required]` attribut sur la propriété de navigation du formateur pour spécifier qu’il doit y avoir un formateur connexe, mais vous n’êtes pas obligé de le faire, car le `InstructorID` clé étrangère (qui est également la clé pour cette table) est non nullable.

## <a name="modify-the-course-entity"></a>Modifier l’entité de cours

![Entité de cours](complex-data-model/_static/course-entity.png)

Dans *Models/Course.cs*, remplacez le code que vous avez ajouté précédemment par le code suivant. Les modifications sont mises en surbrillance.

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

L’entité de cours possède une propriété de clé étrangère `DepartmentID` qui pointe vers l’entité de service associée et il a un `Department` propriété de navigation.

Entity Framework ne requiert pas vous permet d’ajouter une propriété de clé étrangère à votre modèle de données lorsque vous disposez d’une propriété de navigation pour une entité associée.  EF crée des clés étrangères dans la base de données partout où elles sont nécessaires et crée automatiquement [occulter les propriétés](https://docs.microsoft.com/ef/core/modeling/shadow-properties) pour eux. Mais ayant la clé étrangère dans le modèle de données peut rendre les mises à jour plus simple et plus efficace. Par exemple, lorsque vous lisez une entité de cours à modifier, l’entité du service est null si vous ne le charge pas, par conséquent, lorsque vous mettez à jour l’entité de cours, vous devez tout d’abord récupérer l’entité du service. Lorsque la propriété de clé étrangère `DepartmentID` est inclus dans le modèle de données, vous n’avez pas besoin récupérer l’entité du service avant de mettre à jour.

### <a name="the-databasegenerated-attribute"></a>L’attribut DatabaseGenerated

Le `DatabaseGenerated` d’attribut avec le `None` paramètre sur le `CourseID` propriété spécifie que les valeurs de clé primaire sont fournies par l’utilisateur au lieu de générés par la base de données.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Par défaut, Entity Framework suppose que les valeurs de clés primaires sont générées par la base de données. C’est ce que vous souhaitez dans la plupart des scénarios. Toutefois, pour les entités de cours, vous allez utiliser un numéro spécifié par l’utilisateur comme une série de 1000 pour un service, une série de 2000 pour un autre service et ainsi de suite.

Le `DatabaseGenerated` attribut peut également être utilisé pour générer des valeurs par défaut, dans le cas des colonnes de base de données utilisées pour enregistrer la date d’une ligne a été créée ou mis à jour.  Pour plus d’informations, consultez [généré des propriétés](https://docs.microsoft.com/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Propriétés de navigation et de clé étrangère

Les propriétés de clé étrangère et les propriétés de navigation dans l’entité de cours reflètent les relations suivantes :

Un cours est affecté à un service, donc il est un `DepartmentID` clé étrangère et un `Department` propriété de navigation pour les raisons mentionnées ci-dessus.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Un cours peut avoir n’importe quel nombre d’élèves inscrits, par conséquent, le `Enrollments` propriété de navigation est une collection :

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Un cours peut être dirigée par des instructeurs plusieurs, donc la `CourseAssignments` propriété de navigation est une collection (le type `CourseAssignment` est expliquée [ultérieurement](#many-to-many-relationships)) :

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-the-department-entity"></a>Créer l’entité du service

![Entité de service](complex-data-model/_static/department-entity.png)


Créer *Models/Department.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>L’attribut de colonne

Précédemment, vous avez utilisé le `Column` attribut pour modifier le mappage de nom de colonne. Dans le code de l’entité du service, le `Column` attribut sert à modifier SQL type correspondance de données afin que la colonne sera définie à l’aide du type de money SQL Server dans la base de données :

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mappage de colonnes n’est généralement pas nécessaire, car l’Entity Framework choisit le type de données SQL Server approprié en fonction du type CLR que vous définissez pour la propriété. Le CLR `decimal` type correspond à un serveur SQL Server `decimal` type. Mais dans ce cas vous savez que la colonne organiserons des montants en devise et le type de données money est plus adapté.

### <a name="foreign-key-and-navigation-properties"></a>Propriétés de navigation et de clé étrangère

Les propriétés de navigation et de clé étrangère reflètent les relations suivantes :

Un service peut ou ne peut pas avoir un administrateur, et un administrateur est toujours un formateur. Par conséquent la `InstructorID` propriété n’est incluse en tant que clé étrangère à l’entité Instructor, et un point d’interrogation est ajouté après le `int` désignation pour marquer la propriété Nullable de type. La propriété de navigation est nommée `Administrator` , mais sa valeur est une entité Instructor :

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Un service peut avoir de nombreux cours, est une propriété de navigation du cours :

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> Par convention, Entity Framework permet la suppression en cascade pour les clés étrangères non nullables et pour les relations plusieurs-à-plusieurs. Cela peut entraîner des règles de suppression en cascade circulaire, ce qui provoquent une exception lorsque vous essayez d’ajouter une migration. Par exemple, si vous n’avez pas défini la propriété Department.InstructorID Nullable, EF serait configurer une règle de suppression en cascade pour supprimer le formateur lorsque vous supprimez le service, qui n’est pas ce que vous voulez effectuer. Si vos règles d’entreprise nécessaire le `InstructorID` propriété soit non nullable, vous devez utiliser l’instruction API fluent suivante pour désactiver la suppression en cascade sur la relation :
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-the-enrollment-entity"></a>Modifier l’entité de l’inscription

![Entité de l’inscription](complex-data-model/_static/enrollment-entity.png)

Dans *Models/Enrollment.cs*, remplacez le code que vous avez ajouté précédemment par le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Propriétés de navigation et de clé étrangère

Les propriétés de clé étrangère et les propriétés de navigation reflètent les relations suivantes :

Un enregistrement d’inscription est un cours unique, donc il est un `CourseID` propriété de clé étrangère et un `Course` propriété de navigation :

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Un enregistrement d’inscription est pour un étudiant unique, donc il est un `StudentID` propriété de clé étrangère et un `Student` propriété de navigation :

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relations plusieurs-à-plusieurs

Il existe une relation plusieurs-à-plusieurs entre les entités étudiant et cours et l’entité d’inscription fonctionne comme une table de jointure plusieurs-à-plusieurs *avec une charge utile* dans la base de données. « Avec une charge utile » signifie que la table Enrollment contient des données supplémentaires en plus des clés étrangères des tables jointes (dans ce cas, une clé primaire et une propriété de niveau).

L’illustration suivante montre l’aspect de ces relations dans un diagramme d’entité. (Ce diagramme a été généré à l’aide de l’Entity Framework Power Tools pour EF 6.x ; création du diagramme ne fait pas partie de ce didacticiel, il est uniquement utilisé ici à titre d’illustration.)

![Cours de l’étudiant plusieurs à plusieurs relations](complex-data-model/_static/student-course.png)

Chaque ligne de relation comporte un 1 à une extrémité et un astérisque (*) à l’autre, qui indique une relation un-à-plusieurs.

Si la table Enrollment n’a pas été incluent des informations de catégorie, il faudrait uniquement contenir les deux clés étrangères CourseID et StudentID. Dans ce cas, il serait une table de jointure plusieurs-à-plusieurs sans la charge utile (ou une table de jonction pure) dans la base de données. Les entités du formateur et de cours ont ce type de relation plusieurs-à-plusieurs, et l’étape suivante consiste à créer une classe d’entité pour fonctionner comme une table de jonction sans la charge utile.

(EF 6.x prend en charge n’est pas le cas de tables de jointure implicite pour les relations plusieurs-à-plusieurs, mais EF Core. Pour plus d’informations, consultez la [discussion dans le référentiel GitHub de noyaux EF](https://github.com/aspnet/EntityFramework/issues/1368).) 

## <a name="the-courseassignment-entity"></a>L’entité CourseAssignment

![Entité de CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Créer *Models/CourseAssignment.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>Joindre des noms d’entité

Une table de jointure est requis dans la base de données pour la relation plusieurs-à-plusieurs de formateur à cours, et il doit être représenté par un jeu d’entités. Il est courant de nommer une entité de jointure `EntityName1EntityName2`, ce qui serait dans ce cas `CourseInstructor`. Toutefois, nous vous recommandons de choisir un nom qui décrit la relation. Modèles de données démarrent simple et la croissance, avec des jointures non-charge fréquemment mise en route charges utiles ultérieurement. Si vous démarrez avec un nom descriptif d’entité, vous n’aurez pas à le modifier ultérieurement. Dans l’idéal, l’entité de jointure aurait son propre nom (word éventuellement unique) naturelle dans le domaine d’entreprise. Par exemple, la documentation et les clients pu être liées par le biais des évaluations. Pour cette relation, `CourseAssignment` est un meilleur choix que `CourseInstructor`.

### <a name="composite-key"></a>Clé composite

Étant donné que les clés étrangères ne sont pas nullables et ensemble identifie de façon unique identifier chaque ligne de la table, il n’est pas nécessaire pour une clé primaire distincte. Le *InstructorID* et *CourseID* propriétés doivent fonctionner comme une clé primaire composite. La seule façon pour identifier les clés primaires composites EF est à l’aide de la *API fluent* (il ne peut pas être effectuée à l’aide d’attributs). Vous allez apprendre à configurer la clé primaire composite dans la section suivante.

La clé composite permet de s’assurer que vous pouvez avoir plusieurs lignes pour un cours et plusieurs lignes pour un formateur, vous ne peut pas avoir plusieurs lignes pour le même formateur et de cours. Le `Enrollment` jointure entité définit sa propre clé primaire, les doublons de ce type sont possibles. Pour éviter ces doublons, vous pourriez ajouter un index unique sur les champs de clé étrangères, ou configurez `Enrollment` avec une clé primaire composite similaire à `CourseAssignment`. Pour plus d’informations, consultez [index](https://docs.microsoft.com/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Mettre à jour le contexte de base de données

Ajoutez le code en surbrillance suivant à la *Data/SchoolContext.cs* fichier :

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Ce code ajoute de nouvelles entités et configure la clé primaire de l’entité CourseAssignment composite.

## <a name="fluent-api-alternative-to-attributes"></a>Alternative d’API Fluent aux attributs

Le code dans le `OnModelCreating` méthode de la `DbContext` classe utilise le *API fluent* pour configurer le comportement EF. L’API est appelée « fluent », car il est souvent utilisée par tirage une série d’appels de méthode ensemble dans une instruction unique, comme dans cet exemple à partir de la [EF documentations](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration):

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Dans ce didacticiel, vous utilisez l’API fluent uniquement pour le mappage de base de données que vous ne pouvez pas faire avec des attributs. Toutefois, vous pouvez également utiliser l’API fluent pour spécifier la majeure partie de la mise en forme, les règles de validation et mappage que vous pouvez effectuer à l’aide d’attributs. Certains attributs, tels que `MinimumLength` ne peut pas être appliqué avec l’API fluent. Comme mentionné précédemment, `MinimumLength` ne change pas le schéma, elle s’applique uniquement une règle de validation côté client et le serveur.

Certains développeurs préfèrent utiliser l’API fluent exclusivement afin qu’ils peuvent conserver leurs classes d’entité « propre ». Vous pouvez combiner des attributs et des API fluent si vous voulez, et il existe quelques personnalisations peuvent uniquement être effectuées à l’aide des API fluent, mais en général la pratique recommandée consiste à choisir l’une de ces deux approches et utilisez ce constamment autant que possible. Si vous n’utilisez pas les deux, notez que partout où il existe un conflit, API Fluent remplace les attributs.

Pour plus d’informations sur les attributs et l’API fluent, consultez [méthodes de configuration](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration).

## <a name="entity-diagram-showing-relationships"></a>Entité diagramme montrant les relations

L’illustration suivante montre le diagramme Entity Framework Power Tools créer pour le modèle School terminé.

![Diagramme d’entité](complex-data-model/_static/diagram.png)

Outre les lignes de relation un-à-plusieurs (1 à \*), vous pouvez voir ici la ligne de relation d’un-à-zéro-ou-un (1 à 0.. 1) entre les entités du formateur et OfficeAssignment et la ligne zéro-ou-relation un-à-plusieurs (0.. 1 à *) entre le Entités du formateur et de service.

## <a name="seed-the-database-with-test-data"></a>Valeur initiale de la base de données de Test

Remplacez le code dans le *Data/DbInitializer.cs* fichier avec le code suivant afin de fournir des données de la valeur de départ pour les nouvelles entités que vous avez créé.

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

Comme vous l’avez vu dans le premier didacticiel, la majeure partie de ce code crée des objets d’entité simplement et charge les exemples de données dans les propriétés requises pour le test. Notez la façon dont sont gérées les relations plusieurs-à-plusieurs : le code crée des relations en créant des entités dans le `Enrollments` et `CourseAssignment` joindre des jeux d’entités.

## <a name="add-a-migration"></a>Ajouter une migration

Enregistrez vos modifications et générez le projet. Ouvrez la fenêtre de commande dans le dossier du projet, puis entrez la `migrations add` commande (ne le faites pas la commande de base de données de mise à jour encore) :

```console
dotnet ef migrations add ComplexDataModel
```

Vous obtenez un avertissement concernant les pertes de données possibles.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Si vous avez essayé d’exécuter le `database update` commande à ce stade (ne le faites pas encore), vous obtenez l’erreur suivante :

> L’instruction ALTER TABLE est en conflit avec la contrainte de clé étrangère « FK_dbo. Course_dbo. Department_DepartmentID ». Le conflit s’est produite dans la base de données « ContosoUniversity », table « dbo. Département », colonne « DepartmentID ».

Parfois, lorsque vous exécutez des migrations avec des données existantes, vous devez insérer des données de stub dans la base de données pour répondre aux contraintes de clé étrangère. Le code généré dans le `Up` méthode ajoute une clé étrangère DepartmentID non nullable pour la table Course. S’il existe déjà lignes dans la table Course lorsque le code s’exécute, le `AddColumn` opération échoue, car SQL Server ne sait pas quelle valeur à placer dans la colonne ne peut pas être null. Pour ce didacticiel, vous allez exécuter la migration sur une base de données, mais dans une application de production, vous devez effectuer la migration de gérer les données existantes, afin des instructions suivantes montrent un exemple de procédure à suivre.

Pour rendre cette migration fonctionne avec des données existantes, que vous devez modifier le code pour permettre à la nouvelle colonne à une valeur par défaut et créer un service de stub nommé « Temp » en tant que le service par défaut. Par conséquent, cours lignes existantes sont tous les liées au service « Temp » après le `Up` s’exécute la méthode.

* Ouvrez le *{timestamp}_ComplexDataModel.cs* fichier. 

* Commentez la ligne de code qui ajoute la colonne DepartmentID de la table Course.

  [!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Ajoutez le code en surbrillance suivant après le code qui crée la table Department :

  [!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Dans une application de production, vous devez écrire le code ou des scripts pour ajouter des lignes de service et de relations avec les lignes de cours pour les nouvelles lignes de service. Vous ne sont plus devez alors le service « Temp » ou la valeur par défaut sur la colonne Course.DepartmentID.

Enregistrez vos modifications et générez le projet.

## <a name="change-the-connection-string-and-update-the-database"></a>Modifier la chaîne de connexion et de mettre à jour de la base de données

Vous avez maintenant nouveau code la `DbInitializer` classe qui ajoute des données de départ pour les nouvelles entités à une base de données vide. Pour rendre EF à créer une base de données vide, modifier le nom de la base de données dans la chaîne de connexion dans *appsettings.json* ContosoUniversity3 ou un autre nom que vous avez utilisé sur l’ordinateur que vous utilisez.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

Enregistrer les modifications à votre *appsettings.json*.

> [!NOTE]
> Comme alternative à la modification du nom de la base de données, vous pouvez supprimer la base de données. Utilisez **l’Explorateur d’objets SQL Server** (SSOX) ou le `database drop` commande CLI :
> ```console
> dotnet ef database drop
> ```

Une fois que vous avez modifié le nom de la base de données ou supprimés de la base de données, exécuter le `database update` dans la fenêtre de commande à exécuter les migrations.

```console
dotnet ef database update
```

Exécuter l’application pour provoquer le `DbInitializer.Initialize` méthode à exécuter et de remplir la nouvelle base de données.

Ouvrez la base de données dans SSOX comme vous l’avez fait précédemment, puis développez le **Tables** nœud pour voir que toutes les tables ont été créés. (Si vous avez toujours SSOX ouvrir à partir de l’état antérieur, cliquez sur le **Actualiser** bouton.)

![Tables de SSOX](complex-data-model/_static/ssox-tables.png)

Exécutez l’application pour déclencher le code de l’initialiseur qui alimente la base de données.

Avec le bouton droit le **CourseAssignment** de table et sélectionnez **des données d’affichage** pour vérifier qu’il comporte des données.

![Données CourseAssignment dans SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="summary"></a>Résumé

Vous avez maintenant un modèle de données plus complexe et de la base de données correspondante. Dans ce didacticiel, vous en apprendrez davantage sur la façon d’accéder aux données associées.

>[!div class="step-by-step"]
[Précédent](migrations.md)
[Suivant](read-related-data.md)  
