---
title: "Pages Razor avec EF Core - modèle de données - 5 de 8"
author: rick-anderson
description: "Dans ce didacticiel, vous ajoutez des entités et relations et personnalisez le modèle de données en spécifiant la mise en forme, la validation et les règles de mappage de base de données."
keywords: "Annotations de données ASP.NET Core, Entity Framework Core,"
ms.author: riande
manager: wpickett
ms.date: 10/25/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: c2761f79fa4836d29541526782969bb0fd47778b
ms.sourcegitcommit: 6e46abd65973dea796d364a514de9ec2e3e1c1ed
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2017
---
# <a name="creating-a-complex-data-model---ef-core-with-razor-pages-tutorial-5-of-8"></a>Création d’un modèle de données complexes - Core EF avec le didacticiel de Pages Razor (5 de 8)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

Les didacticiels précédents travaillé avec un modèle de base de données qui a été composé de trois entités. Dans ce didacticiel :

* Plusieurs entités et les relations sont ajoutées.
* Le modèle de données est personnalisé en spécifiant la mise en forme, la validation et les règles de mappage de base de données.

Les classes d’entité pour le modèle de données est indiqué dans l’illustration suivante :

![Diagramme d’entité](complex-data-model/_static/diagram.png)

Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez le [application terminée pour cette étape](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part5-complex).

## <a name="customize-the-data-model-with-attributes"></a>Personnaliser le modèle de données avec des attributs

Dans cette section, le modèle de données personnalisé à l’aide d’attributs.

### <a name="the-datatype-attribute"></a>L’attribut de type de données

Actuellement, les pages de l’étudiant affiche l’heure de la date d’inscription. En règle générale, les champs de date ne montrent que la date et non à l’heure.

Mise à jour *Models/Student.cs* avec les éléments suivants en surbrillance le code :

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

Le [DataType](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribut spécifie un type de données qui est plus spécifique que le type intrinsèque de la base de données. Dans ce cas que la date doit être affichée, pas la date et l’heure. Le [énumération DataType](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) fournit de nombreux types de données, telles que Date, heure, numéro de téléphone, devise, EmailAddress, etc. Le `DataType` attribut peut également permettre à l’application fournir automatiquement les fonctionnalités spécifiques au type. Exemple :

* Le `mailto:` lien est automatiquement créé pour `DataType.EmailAddress`.
* Le sélecteur de date est fourni pour `DataType.Date` dans la plupart des navigateurs.

Le `DataType` émet un attribut HTML 5 `data-` attributs (données prononcé tiret) qui utilisent des navigateurs de HTML 5. Le `DataType` attributs ne fournissent pas de validation.

`DataType.Date` ne spécifie pas le format de la date qui s’affiche. Par défaut, le champ de date est affiché selon les formats par défaut basés sur le serveur [CultureInfo](https://docs.microsoft.com/aspnet/core/fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).

L’attribut `DisplayFormat` est utilisé pour spécifier explicitement le format de date :

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Le `ApplyFormatInEditMode` paramètre spécifie que la mise en forme doit également être appliqué à l’interface utilisateur de modification. Certains champs ne doivent pas utiliser `ApplyFormatInEditMode`. Par exemple, le symbole de devise ne doit généralement pas être affiché dans une zone de texte d’édition.

Le `DisplayFormat` attribut peut être utilisé par lui-même. Il est généralement judicieux d’utiliser le `DataType` d’attribut avec le `DisplayFormat` attribut. Le `DataType` attribut transmet la sémantique des données par opposition à comment effectuer le rendu sur un écran. Le `DataType` attribut offre les avantages suivants ne sont pas disponibles dans `DisplayFormat`:

* Le navigateur peut activer des fonctionnalités HTML5. Par exemple, afficher un contrôle calendrier, le symbole monétaire approprié de paramètres régionaux, des liens de messagerie, la validation d’entrée côté client, etc.
* Par défaut, le navigateur restitue les données en utilisant le format approprié en fonction des paramètres régionaux.

Pour plus d’informations, consultez la [ \<d’entrée > documentation de l’application d’assistance de balise](xref:mvc/views/working-with-forms#the-input-tag-helper).

Exécutez l’application. Accédez à la page d’Index d’étudiants. Heures ne sont plus affichés. Toutes les vues qui utilisent le `Student` modèle affiche la date sans heure.

![Affichage des dates sans heures dans la page d’index les étudiants](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>L’attribut StringLength

Les règles de validation de données et messages d’erreur de validation peuvent être spécifiés avec des attributs. Le [StringLength](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribut spécifie la longueur minimale et maximale de caractères autorisés dans un champ de données. Le `StringLength` attribut fournit également la validation côté client et côté serveur. La valeur minimale n’a aucun impact sur le schéma de base de données.

Mise à jour le `Student` modèle avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Le code précédent limite les noms et pas plus de 50 caractères. Le `StringLength` attribut n’empêche pas un utilisateur d’entrer un espace blanc pour un nom. Le [RegularExpression](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribut est utilisé pour appliquer des restrictions à l’entrée. Par exemple, le code suivant requiert le premier caractère en majuscules et les autres caractères alphabétique :

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z''-'\s]*$")]
```

Exécutez l’application :

* Accédez à la page d’étudiants.
* Sélectionnez **créer un nouveau**et entrez un nom de plus de 50 caractères.
* Sélectionnez **créer**, la validation côté client affiche un message d’erreur.

![Page affichant les erreurs de longueur de chaîne d’index les étudiants](complex-data-model/_static/string-length-errors.png)

Dans **l’Explorateur d’objets SQL Server** (SSOX), ouvrez le Concepteur de tables Student en double-cliquant sur le **étudiant** table.

![Table d’étudiants dans SSOX avant la migration](complex-data-model/_static/ssox-before-migration.png)

L’illustration précédente montre le schéma pour le `Student` table. Les champs nom ont type `nvarchar(MAX)` , car les migrations n’a pas été exécuté sur la base de données. Lors de la migration est exécutées plus loin dans ce didacticiel, les champs de nom deviennent `nvarchar(50)`.

### <a name="the-column-attribute"></a>L’attribut de colonne

Les attributs peuvent contrôler comment les classes et les propriétés sont mappées à la base de données. Dans cette section, le `Column` attribut est utilisé pour mapper le nom de la `FirstMidName` propriété « FirstName » dans la base de données.

Lors de la création de la base de données, les noms de propriété sur le modèle sont utilisés pour les noms de colonne (sauf lorsque le `Column` attribut est utilisé).

Le `Student` modèle utilise `FirstMidName` pour le prénom, car le champ peut également contenir un deuxième prénom.

Mise à jour la *Student.cs* fichier avec le code en surbrillance suivant :

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Avec la modification précédente, `Student.FirstMidName` dans l’application est mappée à la `FirstName` colonne de la `Student` table.

L’ajout de la `Column` attribut modifie la sauvegarde du modèle le `SchoolContext`. La sauvegarde du modèle le `SchoolContext` ne correspond plus à la base de données. Si l’application est exécutée avant d’appliquer des migrations, l’exception suivante est générée :

```SQL
SqlException: Invalid column name 'FirstName'.
```
Pour mettre à jour la base de données :

* Générez le projet.
* Ouvrez une fenêtre de commande dans le dossier du projet. Entrez les commandes suivantes pour créer une nouvelle migration et mise à jour de la base de données :

    ```console
    dotnet ef migrations add ColumnFirstName
    dotnet ef database update
    ```

Le `dotnet ef migrations add ColumnFirstName` commande génère le message d’avertissement suivant :

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

L’avertissement est généré, car les champs de nom sont désormais limité à 50 caractères. Si un nom dans la base de données avait plus de 50 caractères, le 51 jusqu’au dernier caractère a été perdue.

* Tester l’application.

Ouvrez la table de l’étudiant dans SSOX :

![Table d’étudiants dans SSOX après la migration](complex-data-model/_static/ssox-after-migration.png)

Avant de la migration a été appliquée, les colonnes Nom étaient de type [nvarchar (max)](https://docs.microsoft.com/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Les colonnes de nom sont maintenant `nvarchar(50)`. Le nom de la colonne a changé de `FirstMidName` à `FirstName`.

> [!Note]
> Dans la section suivante, la génération de l’application à certaines étapes génère les erreurs du compilateur. Les instructions de spécifient le moment générer l’application.

## <a name="student-entity-update"></a>Mise à jour des entités étudiant

![Entité Student](complex-data-model/_static/student-entity.png)

Mise à jour *Models/Student.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>L’attribut requis

Le `Required` attribut rend les champs obligatoires de propriétés de nom. Le `Required` attribut n’est pas nécessaire pour les types tels que des types valeur non nullable (`DateTime`, `int`, `double`, etc.). Les types qui ne peut pas être null sont traitées automatiquement en tant que champs requis.

Le `Required` attribut peut être remplacé par un paramètre de longueur minimale dans le `StringLength` attribut :

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>L’attribut d’affichage

Le `Display` attribut spécifie que la légende pour les zones de texte doit être « Prénom », « Last Name », « Nom complet » et « Date d’inscription. » Légendes par défaut ne dispose pas d’espace divisant les mots, par exemple « nom ».

### <a name="the-fullname-calculated-property"></a>La propriété FullName calculée

`FullName`est une propriété calculée qui retourne une valeur qui est obtenue par concaténation de deux autres propriétés. `FullName`ne peut pas être défini, qu'il a uniquement un accesseur get. Ne `FullName` colonne est créée dans la base de données.

## <a name="create-the-instructor-entity"></a>Créer l’entité Instructor

![Entité Instructor](complex-data-model/_static/instructor-entity.png)

Créer *Models/Instructor.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Notez que plusieurs propriétés sont identiques dans les `Student` et `Instructor` entités. Dans le didacticiel de mise en œuvre de l’héritage, plus loin dans cette série, ce code est refactorisé pour éliminer la redondance.

Plusieurs attributs peuvent être sur une seule ligne. Le `HireDate` attributs peut être écrite comme suit :

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Les propriétés de navigation CourseAssignments et OfficeAssignment

Le `CourseAssignments` et `OfficeAssignment` propriétés sont des propriétés de navigation.

Formateur pouvez animer n’importe quel nombre de cours, de sorte que `CourseAssignments` est défini comme une collection.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Si une propriété de navigation conserve plusieurs entités :

* Il doit être un type de liste dans laquelle les entrées peuvent être ajoutées, supprimées et mis à jour.

Types de propriétés de navigation sont les suivantes :

* `ICollection<T>`
*  `List<T>`
*  `HashSet<T>`

Si `ICollection<T>` est spécifié, EF Core crée un `HashSet<T>` collection par défaut.

Le `CourseAssignment` entité est expliquée dans la section sur les relations plusieurs-à-plusieurs.

État qu’un formateur peut avoir au plus un bureau des règles d’entreprise de l’Université de Contoso. Le `OfficeAssignment` propriété contient un seul `OfficeAssignment` entité. `OfficeAssignment`a la valeur null si aucune office n’est affecté.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>Créer l’entité OfficeAssignment

![Entité OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Créer *Models/OfficeAssignment.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>L’attribut de clé

Le `[Key]` attribut est utilisé pour identifier une propriété comme clé primaire (PK) lorsque le nom de propriété est un élément autre que classnameID ou de code.

Il existe une relation un-à-zéro-ou-un entre le `Instructor` et `OfficeAssignment` entités. Une attribution existe uniquement en relation avec le formateur qu'auquel elle est assignée. Le `OfficeAssignment` PK est également sa clé étrangère (FK) pour le `Instructor` entité. EF principal ne peut pas reconnaître automatiquement `InstructorID` en tant que la clé publique de `OfficeAssignment` , car :

* `InstructorID`ne suit pas la convention d’affectation de noms ID ou classnameID.

Par conséquent, le `Key` attribut est utilisé pour identifier les `InstructorID` en tant que la clé publique :

```csharp
[Key]
public int InstructorID { get; set; }
```

Par défaut, EF Core traite la touche non généré à la base de données, car la colonne est pour une relation d’identification.

### <a name="the-instructor-navigation-property"></a>La propriété de navigation de formateur

Le `OfficeAssignment` propriété de navigation pour la `Instructor` entité est nullable, car :

* Les types référence (tels que les classes sont nullables).
* Formateur n’est peut-être pas une attribution.


Le `OfficeAssignment` entité a non-nullable `Instructor` propriété de navigation, car :

* `InstructorID`est non nullable.
* Une attribution ne peut pas exister sans un formateur.

Lorsqu’un `Instructor` entité associée à une `OfficeAssignment` entité, chaque entité a une référence à l’autre dans sa propriété de navigation.

Le `[Required]` attribut peut être appliqué à la `Instructor` propriété de navigation :

```csharp
[Required]
public Instructor Instructor { get; set; }
```

Le code précédent Spécifie qu’il doit y avoir un formateur connexe. Le code précédent n’est pas nécessaire, car le `InstructorID` clé étrangère (qui est également la clé publique) est non nullable.

## <a name="modify-the-course-entity"></a>Modifier l’entité de cours

![Entité de cours](complex-data-model/_static/course-entity.png)

Mise à jour *Models/Course.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Le `Course` entité a une propriété de clé étrangère (FK) `DepartmentID`. `DepartmentID`pointe vers le `Department` entité. Le `Course` entité a un `Department` propriété de navigation.

EF Core ne nécessite pas une propriété FK pour un modèle de données lorsque le modèle a une propriété de navigation pour une entité associée.

EF Core crée automatiquement les clés étrangères dans la base de données là où elles sont requises. EF Core crée [occulter les propriétés](https://docs.microsoft.com/ef/core/modeling/shadow-properties) pour les clés étrangères du créés automatiquement. La présence de la clé étrangère dans le modèle de données peut simplifier les mises à jour et plus efficace. Par exemple, considérez un modèle où la propriété FK `DepartmentID` est *pas* inclus. Lorsqu’une entité de cours est extraite à modifier :

* Le `Department` entité a la valeur null s’il n’est pas explicitement chargé.
* Pour mettre à jour de l’entité de cours, le `Department` entité doit tout d’abord être extraites.

Lorsque la propriété FK `DepartmentID` est inclus dans le modèle de données, il n’est pas nécessaire d’extraire le `Department` entité avant une mise à jour.

### <a name="the-databasegenerated-attribute"></a>L’attribut DatabaseGenerated

Le `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribut spécifie que la clé est fournie par l’application au lieu de générés par la base de données.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Par défaut, EF Core suppose que les valeurs de clé primaire sont générés par la base de données. Base de données généré PK valeurs est généralement la meilleure approche. Pour `Course` entités, l’utilisateur spécifie le PK. Par exemple, un nombre de cours comme une série de 1000 pour le service de mathématiques, une série de 2000 pour le service en anglais.

Le `DatabaseGenerated` attribut peut également être utilisé pour générer des valeurs par défaut. Par exemple, la base de données peut générer automatiquement un champ de date pour enregistrer la date d’une ligne a été créée ou mis à jour. Pour plus d’informations, consultez [généré des propriétés](https://docs.microsoft.com/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Propriétés de navigation et de clé étrangère

Les propriétés de clé étrangère (FK) et les propriétés de navigation dans les `Course` entité reflète les relations suivantes :

Un cours est affecté à un service, donc il est un `DepartmentID` FK et un `Department` propriété de navigation.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Un cours peut avoir n’importe quel nombre d’élèves inscrits, par conséquent, le `Enrollments` propriété de navigation est une collection :

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Un cours peut être dirigée par des instructeurs plusieurs, donc la `CourseAssignments` propriété de navigation est une collection :

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`est expliquée [ultérieurement](#many-to-many-relationships).

## <a name="create-the-department-entity"></a>Créer l’entité du service

![Entité de service](complex-data-model/_static/department-entity.png)

Créer *Models/Department.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>L’attribut de colonne

Précédemment le `Column` attribut a été utilisé pour modifier le mappage de nom de colonne. Dans le code de la `Department` entité, le `Column` attribut est utilisé pour modifier le mappage de type de données SQL. Le `Budget` colonne est définie à l’aide du type de money SQL Server dans la base de données :

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mappage de colonnes n’est généralement pas nécessaire. EF Core choisit généralement le type de données SQL Server approprié en fonction du type CLR pour la propriété. Le CLR `decimal` type correspond à un serveur SQL Server `decimal` type. `Budget`est de devise, et le type de données money est plus approprié pour la devise.

### <a name="foreign-key-and-navigation-properties"></a>Propriétés de navigation et de clé étrangère

Les propriétés de navigation et FK reflètent les relations suivantes :

* Un service peut ou ne peut pas avoir un administrateur.
* Un administrateur est toujours un formateur. Par conséquent la `InstructorID` propriété n’est incluse en tant que la clé étrangère pour la `Instructor` entité.

La propriété de navigation est nommée `Administrator` mais contient un `Instructor` entité :

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Le point d’interrogation ( ?) dans le code précédent Spécifie que la propriété est nullable.

Un service peut avoir de nombreux cours, est une propriété de navigation du cours :

```csharp
public ICollection<Course> Courses { get; set; }
```

Remarque : Par convention, EF Core permet de suppression en cascade pour les clés étrangères du non nullable et pour les relations plusieurs-à-plusieurs. Les règles de suppression en cascade circulaire peut entraîner une suppression en cascade. Circulaire suppression en cascade entraîne des règles d’exception lors de l’ajout d’une migration.

Par exemple, si le `Department.InstructorID` propriété n’a pas a été définie comme nullable :

* EF Core configure une règle de suppression en cascade pour supprimer le formateur lorsque le service est supprimé.
* La suppression du formateur lorsque le service est supprimé n’est pas du comportement souhaité.

Si les règles d’entreprise nécessaire le `InstructorID` propriété être non nullable, utilisez l’instruction d’API fluent suivante :

 ```csharp
 modelBuilder.Entity<Department>()
    .HasOne(d => d.Administrator)
    .WithMany()
    .OnDelete(DeleteBehavior.Restrict)
 ```

Le code précédent désactive la suppression en cascade sur la relation de formateur de service.

## <a name="update-the-enrollment-entity"></a>Mise à jour de l’entité de l’inscription

Un enregistrement d’inscription concerne un un cours effectuée par un étudiant.

![Entité de l’inscription](complex-data-model/_static/enrollment-entity.png)

Mise à jour *Models/Enrollment.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Propriétés de navigation et de clé étrangère

Les propriétés de navigation et les propriétés FK reflètent les relations suivantes :

Un enregistrement d’inscription est pour un cours, donc il est un `CourseID` les propriété FK et un `Course` propriété de navigation :

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Un enregistrement d’inscription est pour un étudiant, donc il est un `StudentID` les propriété FK et un `Student` propriété de navigation :

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relations plusieurs-à-plusieurs

Il existe une relation plusieurs-à-plusieurs entre la `Student` et `Course` entités. Le `Enrollment` entité fonctionne comme une table de jointure plusieurs-à-plusieurs *avec une charge utile* dans la base de données. « Avec une charge utile » signifie que la `Enrollment` table contient des données supplémentaires en plus des clés étrangères pour les tables jointes (dans ce cas, les clés publiques et `Grade`).

L’illustration suivante montre l’aspect de ces relations dans un diagramme d’entité. (Ce diagramme a été généré à l’aide de EF Power Tools pour EF 6.x. Créer le diagramme ne fait pas partie de ce didacticiel.)

![Cours de l’étudiant plusieurs à plusieurs relations](complex-data-model/_static/student-course.png)

Chaque ligne de relation comporte un 1 à une extrémité et un astérisque (*) à l’autre, qui indique une relation un-à-plusieurs.

Si le `Enrollment` table n’a pas inclure les informations de catégorie, il doit uniquement contenir le clés étrangères deux du (`CourseID` et `StudentID`). Une table de jointure plusieurs-à-plusieurs sans la charge utile est parfois appelée une table de jonction pure (PJT).

Le `Instructor` et `Course` les entités ont une relation plusieurs-à-plusieurs à l’aide d’une table de jonction pure.

Remarque : EF 6.x prend en charge n’est pas le cas de tables de jointure implicite pour les relations plusieurs-à-plusieurs, mais EF Core. Pour plus d’informations, consultez [plusieurs-à-plusieurs relations dans EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>L’entité CourseAssignment

![Entité de CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Créer *Models/CourseAssignment.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Au cours de formateur

![Formateur à cours m:M](complex-data-model/_static/courseassignment.png)

La relation plusieurs-à-plusieurs de formateur à cours :

* Requiert une table de jointure qui doit être représentée par un jeu d’entités.
* Est une table de jonction pure (table sans la charge utile).

Il est courant de nommer une entité de jointure `EntityName1EntityName2`. Par exemple, la table de jointure de formateur à cours à l’aide de ce modèle est `CourseInstructor`. Toutefois, nous recommandons à l’aide d’un nom qui décrit la relation.

Modèles de données démarrent simple et croître. Les jointures non-charge utile (PJTs) évoluent fréquemment pour inclure la charge utile. En commençant par un nom descriptif d’entité, le nom n’a pas besoin modifier lorsque la table de jointure change. Dans l’idéal, l’entité de jointure aurait son propre nom (word éventuellement unique) naturelle dans le domaine d’entreprise. Par exemple, la documentation et les clients pu être liés à une entité de jointure appelée contrôle d’accès. Pour la relation plusieurs-à-plusieurs de formateur à cours, `CourseAssignment` vaut mieux utiliser `CourseInstructor`.

### <a name="composite-key"></a>Clé composite

Clés étrangères du n’est pas nullable. Le deux clés étrangères dans `CourseAssignment` (`InstructorID` et `CourseID`) ensemble identifier de façon unique chaque ligne de la `CourseAssignment` table. `CourseAssignment`ne nécessite pas un PK de dédié. Le `InstructorID` et `CourseID` propriétés fonctionnent comme un composite PK. La seule façon de spécifier primaires composites EF cœur est avec le *API fluent*. La section suivante montre comment configurer le PK de composite.

La clé composite garantit :

* Plusieurs lignes sont autorisées pour un cours.
* Plusieurs lignes sont autorisées pour un formateur.
* Plusieurs lignes pour la même formateur et cours n’est pas autorisée.

Le `Enrollment` jointure entité définit sa propre clé de performance, les doublons de ce type sont possibles. Pour éviter ces doublons :

* Ajouter un index unique sur les champs FK, ou
* Configurer `Enrollment` avec une clé primaire composite similaire à `CourseAssignment`. Pour plus d’informations, consultez [index](https://docs.microsoft.com/ef/core/modeling/indexes).

## <a name="update-the-db-context"></a>Contexte de la mise à jour la base de données

Ajoutez le code en surbrillance suivant à *Data/SchoolContext.cs*:

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Le code précédent ajoute de nouvelles entités et configure le `CourseAssignment` PK. composite de l’entité

## <a name="fluent-api-alternative-to-attributes"></a>Alternative d’API Fluent aux attributs

Le `OnModelCreating` méthode dans l’exemple précédent de code utilise le *API fluent* pour configurer le comportement EF principal. L’API est appelée « fluent », car il est souvent utilisée par tirage une série d’appels de méthode dans une instruction unique. Le [suivant code](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration) est un exemple de l’API fluent :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Dans ce didacticiel, l’API fluent est utilisée uniquement pour le mappage de base de données qui ne peut pas être effectuée avec des attributs. Toutefois, l’API fluent peut spécifier la majeure partie de la mise en forme, les règles de validation et mappage qui peuvent être effectuées avec des attributs.

Certains attributs, tels que `MinimumLength` ne peut pas être appliqué avec l’API fluent. `MinimumLength`ne modifiez pas le schéma, elle s’applique uniquement une règle de validation de longueur minimale.

Certains développeurs préfèrent utiliser l’API fluent exclusivement afin qu’ils peuvent conserver leurs classes d’entité « propre ». Attributs et l’API fluent peuvent être mélangés. Il existe des configurations qui peuvent uniquement être effectuées avec l’API fluent (spécification d’une clé primaire composite). Certaines configurations qui peut uniquement être effectuée avec des attributs (`MinimumLength`). La méthode recommandée pour l’utilisation des API fluent ou attributs :

* Choisissez une de ces deux approches.
* Utiliser l’approche choisie constamment autant que possible.

Certains des attributs utilisés dans ce didacticiel sont utilisées pour :

* Validation uniquement (par exemple, `MinimumLength`).
* Configuration de base EF uniquement (par exemple, `HasKey`).
* Configuration de la validation et EF Core (par exemple, `[StringLength(50)]`).

Pour plus d’informations sur les attributs et l’API fluent, consultez [méthodes de configuration](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration).

## <a name="entity-diagram-showing-relationships"></a>Entité diagramme montrant les relations

L’illustration suivante montre le diagramme EF Power Tools créer pour le modèle School terminé.

![Diagramme d’entité](complex-data-model/_static/diagram.png)

Le diagramme précédent montre :

* Plusieurs lignes de relation un-à-plusieurs (1 à \*).
* La ligne de relation d’un-à-zéro-ou-un (1 à 0. 1) entre la `Instructor` et `OfficeAssignment` entités.
* La ligne zéro-ou-relation un-à-plusieurs (0. 1 à *) entre la `Instructor` et `Department` entités.

## <a name="seed-the-db-with-test-data"></a>Valeur initiale de la base de données avec des données de Test

Mettre à jour le code dans *Data/DbInitializer.cs*:

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

Le code précédent fournit des données de valeur initiale pour les nouvelles entités. La majeure partie de ce code crée des objets d’entité et charge les exemples de données. Les exemples de données sont utilisés pour le test. Le code précédent crée les relations plusieurs-à-plusieurs suivantes :

* `Enrollments`
* `CourseAssignment`

Remarque : [EF Core 2.1](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap) prend en charge [amorçage de données](https://github.com/aspnet/EntityFrameworkCore/issues/629).

## <a name="add-a-migration"></a>Ajouter une migration

Générez le projet. Ouvrez une fenêtre de commande dans le dossier du projet, puis entrez la commande suivante :

```console
dotnet ef migrations add ComplexDataModel
```

La commande précédente affiche un avertissement concernant les pertes de données possibles.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Si la `database update` commande est exécutée, l’erreur suivante se produit :

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

Lorsque les migrations sont exécutées avec des données existantes, il peut y avoir des contraintes FK qui ne sont pas remplies avec les données de sortie. Pour ce didacticiel, une nouvelle base de données est créé, il n’y a aucune violation de contrainte FK. Consultez [fixation des contraintes de clé étrangère avec des données héritées](#fk) pour obtenir des instructions sur la façon de corriger les violations FK sur la base de données actuelle.

## <a name="change-the-connection-string-and-update-the-db"></a>Modifier la chaîne de connexion et de mettre à jour de la base de données

Le code de mise à jour `DbInitializer` ajoute des données de valeur initiale pour les nouvelles entités. Pour forcer Core EF pour créer une nouvelle base de données vide :

* Modifier le nom de chaîne de connexion de base de données dans *appsettings.json* à ContosoUniversity3. Le nouveau nom doit être un nom qui n’a pas été utilisé sur l’ordinateur.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
      },
    ```

* Vous pouvez également supprimer la base de données à l’aide de :

    * **L’Explorateur d’objets SQL Server** (SSOX).
    * Le `database drop` commande CLI :

   ```console
   dotnet ef database drop
   ```

Exécutez `database update` dans la fenêtre de commande :

```console
dotnet ef database update
```

La commande précédente s’exécute toutes les migrations.

Exécutez l’application. L’exécution de l’application en cours d’exécution le `DbInitializer.Initialize` (méthode). Le `DbInitializer.Initialize` remplit la nouvelle base de données.

Ouvrez la base de données dans SSOX :

* Développez le **Tables** nœud. Les tables sont affichés.
* Si SSOX a été ouverte, cliquez sur le **Actualiser** bouton.

![Tables de SSOX](complex-data-model/_static/ssox-tables.png)

Examinez le **CourseAssignment** table :

* Avec le bouton droit le **CourseAssignment** de table et sélectionnez **des données d’affichage**.
* Vérifiez le **CourseAssignment** table contient des données.

![Données CourseAssignment dans SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="fk"></a>

## <a name="fixing-foreign-key-constraints-with-legacy-data"></a>Résolution des contraintes de clé étrangère avec des données héritées

Cette section est facultative.

Lorsque les migrations sont exécutées avec des données existantes, il peut y avoir des contraintes FK qui ne sont pas remplies avec les données de sortie. Données de production, les étapes nécessaires pour migrer les données existantes. Cette section fournit un exemple de résolution des violations de contrainte FK. Ne pas rendre ces modifications de code sans une sauvegarde. Ne modifiez ces code si la fin de la section précédente et la mise à jour de la base de données.

Le *{timestamp}_ComplexDataModel.cs* fichier contient le code suivant :

[!code-csharp[Main](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

Le code précédent ajoute un non-nullable `DepartmentID` FK à la `Course` table. Contient des lignes dans la base de données à partir du didacticiel précédent `Course`, de sorte que cette table ne peut pas être mis à jour par des migrations.

Pour rendre la `ComplexDataModel` travail de migration avec des données existantes :

* Modifier le code pour permettre à la nouvelle colonne (`DepartmentID`) comme valeur par défaut.
* Créer un service factice nommé « Temp » en tant que le service par défaut.

### <a name="fix-the-foreign-key-constraints"></a>Résoudre les contraintes de clé étrangères

Mise à jour la `ComplexDataModel` classes `Up` méthode :

* Ouvrez le *{timestamp}_ComplexDataModel.cs* fichier.
* Commentez la ligne de code qui ajoute le `DepartmentID` colonne à la `Course` table.

[!code-csharp[Main](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Ajoutez le code en surbrillance suivant. Le nouveau code va après le `.CreateTable( name: "Department"` bloc :[!code-csharp[Main](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Avec les modifications précédentes, existant `Course` lignes sont liées au service « Temp » après la `ComplexDataModel` `Up` s’exécute la méthode.

Une application de production serait :

* Inclure le code ou des scripts pour ajouter `Department` lignes et liées `Course` lignes à la nouvelle `Department` lignes.
* N’utilisez pas le service « Temp » ou la valeur par défaut `Course.DepartmentID `.

Le didacticiel suivant traite les données associées.

>[!div class="step-by-step"]
[Précédent](xref:data/ef-rp/migrations)
[Suivant](xref:data/ef-rp/read-related-data)