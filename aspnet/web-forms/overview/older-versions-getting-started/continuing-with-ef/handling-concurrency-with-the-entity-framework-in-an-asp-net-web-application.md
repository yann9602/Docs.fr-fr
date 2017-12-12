---
uid: web-forms/overview/older-versions-getting-started/continuing-with-ef/handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application
title: "Gestion d’accès concurrentiel avec Entity Framework 4.0 dans une Application ASP.NET 4 | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels s’appuie sur l’application web de l’université Contoso qui est créée par la prise en main de la série de didacticiels Entity Framework 4.0. JE..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/26/2011
ms.topic: article
ms.assetid: a5aa22a6-fb7f-4f41-9c7f-addda151940b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/continuing-with-ef/handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application
msc.type: authoredcontent
ms.openlocfilehash: 7bdcf610458631749531ed1279d27e90572f0371
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="handling-concurrency-with-the-entity-framework-40-in-an-aspnet-4-web-application"></a>Gestion d’accès concurrentiel avec Entity Framework 4.0 dans une Application ASP.NET 4
====================
Par [Tom Dykstra](https://github.com/tdykstra)

> Cette série de didacticiels s’appuie sur l’application web de l’université Contoso qui est créée par le [mise en route avec l’Entity Framework 4.0](https://asp.net/entity-framework/tutorials#Getting%20Started) série de didacticiels. Si vous n’avez pas terminé les didacticiels antérieures, comme point de départ pour ce didacticiel vous pouvez [télécharger l’application](https://code.msdn.microsoft.com/ASPNET-Web-Forms-97f8ee9a) que vous devriez avoir créé. Vous pouvez également [télécharger l’application](https://code.msdn.microsoft.com/ASPNET-Web-Forms-6c7197aa) qui est créé par la série de didacticiels terminée. Si vous avez des questions sur les didacticiels, vous pouvez les valider pour le [forum de ASP.NET Entity Framework](https://forums.asp.net/1227.aspx).


Le didacticiel précédent vous a montré comment trier et données de filtre à l’aide du `ObjectDataSource` contrôle et Entity Framework. Ce didacticiel montre les options permettant de gérer l’accès concurrentiel dans une application web ASP.NET qui utilise Entity Framework. Vous allez créer une nouvelle page web qui est dédié à la mise à jour des attributions de formateur. Vous allez gérer les problèmes d’accès concurrentiel dans cette page, dans la page Services que vous avez créé précédemment.

[![Image06](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image2.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image1.png)

[![Image01](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image4.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image3.png)

## <a name="concurrency-conflicts"></a>Conflits d’accès concurrentiel

Un conflit de concurrence se produit lorsqu’un utilisateur modifie un enregistrement et un autre utilisateur modifie le même enregistrement, avant la première modification utilisateur est écrit dans la base de données. Si vous ne définissez Entity Framework pour détecter ces conflits, la personne qui met à jour la base de données remplace dernier les modifications de l’autre utilisateur. Dans de nombreuses applications, ce risque est acceptable, et vous n’êtes pas obligé de configurer l’application pour gérer les conflits d’accès concurrentiel possibles. (S’il existe quelques utilisateurs ou les mises à jour, ou si n’est pas réellement critique si des modifications sont remplacées, le coût de la programmation d’accès concurrentiel peut compenser les avantages.) Si vous n’avez pas besoin de vous soucier des conflits d’accès concurrentiel, vous pouvez ignorer ce didacticiel ; les deux didacticiels restants de la série ne dépendent pas tout ce que vous générez dans celle-ci.

### <a name="pessimistic-concurrency-locking"></a>L’accès simultané pessimiste (verrouillage)

Si votre application doit-elle éviter la perte accidentelle de données dans les scénarios d’accès concurrentiel, une manière de procéder consiste à utiliser des verrous de base de données. Il s’agit *d’accès concurrentiel pessimiste*. Par exemple, avant de lire une ligne à partir d’une base de données, vous demandez un verrou de lecture seule ou pour l’accès de mise à jour. Si vous verrouillez une ligne pour l’accès de mise à jour, aucun autre utilisateur n’est autorisés pour le verrouillage de la ligne pour en lecture seule ou mettre à jour de l’accès, car ils obtenez une copie des données sont en cours de modification. Si vous verrouillez une ligne pour l’accès en lecture seule, d’autres peuvent également le verrouiller pour l’accès en lecture seule, mais pas pour la mise à jour.

Gestion des verrous a certains inconvénients. Il peut être complexe au programme. Il nécessite des ressources de gestion de base de données importantes, et cela peut provoquer des problèmes de performances en tant que le nombre d’utilisateurs d’une application augmente (autrement dit, il n’évolue pas correctement). Pour ces raisons, pas tous les systèmes de gestion de base de données prend en charge l’accès simultané pessimiste. Entity Framework ne fournit aucune prise en charge intégrée pour celle-ci, et ce didacticiel ne vous montre comment l’implémenter.

### <a name="optimistic-concurrency"></a>Accès concurrentiel optimiste

L’alternative à l’accès concurrentiel pessimiste est *d’accès concurrentiel optimiste*. L’accès concurrentiel optimiste signifie autoriser les conflits d’accès concurrentiel se produire et puis réagir correctement dans le cas. Par exemple, John exécute le *Department.aspx* page, clique sur le **modifier** la liaison pour le service de l’historique et réduit la **Budget** quantité à partir du format 1,000,000.00 $ $ $ 125,000.00. (John administre un département concurrent et souhaite libérer de l’argent pour son propre service).

[![Image07](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image6.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image5.png)

Avant de Jean clique sur **mise à jour**, Jane exécute la même page, clique sur le **modifier** lien pour le service de l’historique, puis modifie le **Date de début** champ à 1/1/1/10/2011 1999. (Jane administre le service de l’historique et veut afin de lui donner plus d’ancienneté.)

[![Image08](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image8.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image7.png)

John clique sur **mise à jour** tout d’abord, puis clique Jane **mise à jour**. Listes de Jeanne navigateur maintenant la **Budget** montant comme format 1,000,000.00 $, mais cela est incorrect, car la quantité a été modifiée par John à $125,000.00.

Certaines des actions que vous pouvez effectuer dans ce scénario sont les suivantes :

- Vous pouvez effectuer le suivi des dont un utilisateur a modifié la propriété et mettre à jour uniquement les colonnes correspondantes dans la base de données. Dans l’exemple de scénario, aucune donnée n’a été perdue, car des propriétés différentes ont été mis à jour par les deux utilisateurs. La prochaine fois que quelqu'un accède le département de l’historique, ils verront le 1/1/1999 et $125,000.00. 

    Il s’agit du comportement par défaut dans Entity Framework, et elle peut réduire sensiblement le nombre de conflits qui peuvent entraîner une perte de données. Toutefois, ce comportement n’éviter la perte de données si des modifications concurrentes sont apportées à la même propriété d’une entité. En outre, ce comportement n’est pas toujours possible. Lorsque vous mappez des procédures stockées à un type d’entité, toutes les propriétés d’une entité sont mis à jour lorsque des modifications à l’entité sont apportées dans la base de données.
- Vous pouvez laisser les modifications de Jane écrase les modifications de John. Une fois que Jane clique sur **mise à jour**, le **Budget** quantité repasse au format 1,000,000.00 $. Cela s’appelle un *Client Wins* ou *dernier dans Wins* scénario. (Les valeurs du client sont prioritaires sur les nouveautés dans le magasin de données).
- Vous pouvez empêcher la modification de Jeanne à partir de la mise à jour dans la base de données. En règle générale, vous afficher un message d’erreur, elle indique l’état actuel des données et lui permet d’entrer à nouveau des modifications si elle souhaite que pour les rendre encore. Vous pouvez davantage automatiser le processus d’en enregistrant son entrée et de lui donner la possibilité à appliquer à nouveau sans avoir à saisir de nouveau. Cela s’appelle un *magasin Wins* scénario. (Les valeurs du magasin de données sont prioritaires sur les valeurs soumis par le client).

### <a name="detecting-concurrency-conflicts"></a>Détection des conflits d’accès concurrentiel

Dans Entity Framework, vous pouvez résoudre les conflits en gérant `OptimisticConcurrencyException` Entity Framework lève les exceptions. Pour savoir quand ces exceptions de lever, Entity Framework doit être en mesure de détecter les conflits. Par conséquent, vous devez configurer la base de données et le modèle de données en conséquence. Certaines options pour l’activation de la détection de conflit sont les suivantes :

- Dans la base de données inclut une colonne de table qui peut être utilisée pour déterminer quand une ligne a été modifiée. Vous pouvez ensuite configurer l’Entity Framework pour inclure cette colonne dans la `Where` clause SQL `Update` ou `Delete` commandes.

    C’est le rôle de la `Timestamp` colonne dans la `OfficeAssignment` table.

    [![Image09](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image10.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image9.png)

    Type de données de la `Timestamp` colonne est également appelée `Timestamp`. Toutefois, la colonne ne contient pas réellement une valeur de date ou d’heure. Au lieu de cela, la valeur est un numéro séquentiel qui est incrémentée chaque fois que la ligne est mise à jour. Dans un `Update` ou `Delete` commande, le `Where` clause inclut la version d’origine `Timestamp` valeur. Si la ligne mise à jour a été modifiée par un autre utilisateur, la valeur de `Timestamp` est différente de celle de la valeur d’origine, afin que la `Where` clause ne retourne aucune ligne à mettre à jour. Lorsque l’Entity Framework trouve aucune ligne n’ont été mis à jour en cours `Update` ou `Delete` de commandes (autrement dit, lorsque le nombre de lignes affectées est égal à zéro), il qui interprète comme un conflit d’accès concurrentiel.
- Configurer l’Entity Framework pour inclure les valeurs d’origine de chaque colonne dans la table dans le `Where` clause de `Update` et `Delete` commandes.

    Comme dans la première option, si n’importe où dans la ligne a changé depuis la ligne a été lu tout d’abord, le `Where` clause ne retourne pas une ligne à mettre à jour, lequel Entity Framework interprète comme un conflit d’accès concurrentiel. Cette méthode est aussi efficace que d’utiliser un `Timestamp` champ, mais peut être inefficace. Pour les tables de base de données qui ont beaucoup de colonnes, cela peut entraîner de très grande `Where` clauses, et dans une application web peut nécessiter que vous avez de grandes quantités d’état. Maintenance de grandes quantités d’état peut affecter les performances de l’application, car il nécessite des ressources serveur (par exemple, état de session) ou doit être inclus dans la page web elle-même (par exemple, état d’affichage).

Dans ce didacticiel, vous allez ajouter les conflits d’accès concurrentiel optimiste pour une entité qui n’a pas une propriété de suivi de gestion des erreurs (le `Department` entité) et pour une entité qui possède une propriété de suivi (le `OfficeAssignment` entité).

## <a name="handling-optimistic-concurrency-without-a-tracking-property"></a>Gestion de l’accès concurrentiel optimiste sans une propriété de suivi

Pour implémenter l’accès concurrentiel optimiste pour les `Department` entité, qui n’a pas de suivi (`Timestamp`) la propriété, vous effectuerez les tâches suivantes :

- Modifier le modèle de données pour activer l’accès concurrentiel de suivi pour `Department` entités.
- Dans le `SchoolRepository` classe, de gérer les exceptions d’accès concurrentiel dans le `SaveChanges` (méthode).
- Dans le *Departments.aspx* page, gérer les exceptions d’accès concurrentiel en affichant un message à l’utilisateur d’avertissement que les tentatives de modification ont échoué. L’utilisateur peut ensuite consulter les valeurs actuelles et recommencez les modifications s’ils sont toujours nécessaires.

### <a name="enabling-concurrency-tracking-in-the-data-model"></a>L’activation du suivi des modifications dans le modèle de données de concurrence

Dans Visual Studio, ouvrez l’application web de Contoso University que vous utilisiez dans le didacticiel précédent de cette série.

Ouvrez *SchoolModel.edmx*et dans le Concepteur de modèle de données, cliquez sur le `Name` propriété dans le `Department` entité, puis cliquez sur **propriétés**. Dans le **propriétés** fenêtre, de modifier le `ConcurrencyMode` propriété `Fixed`.

[![Image16](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image12.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image11.png)

Faire de même pour les autres propriétés scalaires non de la clé primaire (`Budget`, `StartDate`, et `Administrator`.) (Vous ne pouvez pas faire cela pour les propriétés de navigation.) Spécifie que chaque fois que l’Entity Framework génère un `Update` ou `Delete` commande SQL pour mettre à jour le `Department` entité dans la base de données, ces colonnes (avec des valeurs d’origine) doivent être inclus dans le `Where` clause. Si aucune ligne n’est trouvée quand la `Update` ou `Delete` commande s’exécute, Entity Framework lève une exception d’accès concurrentiel optimiste.

Enregistrez et fermez le modèle de données.

### <a name="handling-concurrency-exceptions-in-the-dal"></a>La gestion des Exceptions d’accès concurrentiel dans la couche DAL

Ouvrez *SchoolRepository.cs* et ajoutez le code suivant `using` instruction pour le `System.Data` espace de noms :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample1.cs)]

Ajoutez le code suivant nouvelle `SaveChanges` méthode, qui gère les exceptions d’accès concurrentiel optimiste :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample2.cs)]

Si une erreur d’accès concurrentiel se produit lorsque cette méthode est appelée, les valeurs de propriété de l’entité dans la mémoire sont remplacées par les valeurs actuellement dans la base de données. L’exception d’accès concurrentiel est à nouveau levée afin que la page web puisse le traiter.

Dans le `DeleteDepartment` et `UpdateDepartment` méthodes, remplacez l’appel existant à `context.SaveChanges()` avec un appel à `SaveChanges()` pour appeler la nouvelle méthode.

### <a name="handling-concurrency-exceptions-in-the-presentation-layer"></a>La gestion des Exceptions d’accès concurrentiel dans la couche présentation

Ouvrez *Departments.aspx* et ajoutez un `OnDeleted="DepartmentsObjectDataSource_Deleted"` attribut le `DepartmentsObjectDataSource` contrôle. La balise d’ouverture du contrôle ressemble maintenant à l’exemple suivant.

[!code-aspx[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample3.aspx)]

Dans le `DepartmentsGridView` contrôler, spécifiez toutes les colonnes de table dans le `DataKeyNames` d’attribut, comme illustré dans l’exemple suivant. Notez que cela crée une vue très volumineuse champs d’état, qui est une bonne raison Pourquoi utiliser un champ de suivi est généralement le meilleur moyen pour effectuer le suivi des conflits d’accès concurrentiel.

[!code-aspx[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample4.aspx)]

Ouvrez *Departments.aspx.cs* et ajoutez le code suivant `using` instruction pour le `System.Data` espace de noms :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample5.cs)]

Ajouter la nouvelle méthode suivante, vous appellerez à partir du contrôle de source de données `Updated` et `Deleted` gestionnaires d’événements pour la gestion des exceptions d’accès concurrentiel :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample6.cs)]

Ce code vérifie le type d’exception, et si elle est une exception d’accès concurrentiel, le code crée dynamiquement un `CustomValidator` contrôle qui à son tour affiche un message dans le `ValidationSummary` contrôle.

Appeler la nouvelle méthode à partir de la `Updated` Gestionnaire d’événements que vous avez ajouté précédemment. En outre, créer un nouveau `Deleted` Gestionnaire d’événements qui appelle la même méthode (mais ne fait rien d’autre) :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample7.cs)]

### <a name="testing-optimistic-concurrency-in-the-departments-page"></a>Test de l’accès concurrentiel optimiste dans la Page services

Exécutez le *Departments.aspx* page.

[![Image17](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image14.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image13.png)

Cliquez sur **modifier** dans une ligne et modifiez la valeur dans la **Budget** colonne. (Souvenez-vous que vous pouvez modifier uniquement les enregistrements que vous avez créée pour ce didacticiel, étant donné qu’existants `School` les enregistrements de base de données contiennent des données non valides. L’enregistrement pour le service de rentabilité est sécurisé à faire des essais avec.)

[![Image18](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image16.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image15.png)

Ouvrez une nouvelle fenêtre de navigateur et exécutez à nouveau la page (copier l’URL à partir de la zone d’adresse de la première fenêtre de navigateur à la deuxième fenêtre de navigateur).

[![Image17](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image18.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image17.png)

Cliquez sur **modifier** dans la même ligne vous modifié précédemment et modifiez le **Budget** valeur à autre chose.

[![Image19](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image20.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image19.png)

Dans la deuxième fenêtre de navigateur, cliquez sur **mise à jour**. Le **Budget** montant est modifié avec succès cette nouvelle valeur.

[![Image20](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image22.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image21.png)

Dans la première fenêtre de navigateur, cliquez sur **mise à jour**. La mise à jour échoue. Le **Budget** quantité s’affiche de nouveau à l’aide de la valeur définie dans la deuxième fenêtre de navigateur, et vous voyez un message d’erreur.

[![Image21](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image24.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image23.png)

## <a name="handling-optimistic-concurrency-using-a-tracking-property"></a>Gestion d’accès concurrentiel optimiste à l’aide d’une propriété de suivi

Pour gérer l’accès concurrentiel optimiste pour une entité qui a une propriété de suivi, vous effectuerez les tâches suivantes :

- Ajouter des procédures stockées pour le modèle de données pour gérer les `OfficeAssignment` entités. (Le suivi des propriétés et des procédures stockées n’êtes pas obligé d’être utilisées ensemble ; ils sont simplement regroupés ensemble ici à titre d’illustration.)
- Ajouter des méthodes CRUD pour la couche DAL et la couche BLL pour `OfficeAssignment` entités, y compris le code pour gérer les exceptions d’accès concurrentiel optimiste dans la couche DAL.
- Créer une page web d’office-affectations.
- Tester l’accès concurrentiel optimiste dans la nouvelle page web.

### <a name="adding-officeassignment-stored-procedures-to-the-data-model"></a>Ajout de OfficeAssignment des procédures stockées pour le modèle de données

Ouvrez le *SchoolModel.edmx* de fichiers dans le Générateur de modèles, cliquez sur l’aire de conception, puis cliquez sur **modèle de mise à jour à partir de la base de données**. Dans le **ajouter** onglet de la **choisir vos objets de base de données** boîte de dialogue, développez **de procédures stockées** et sélectionnez les trois `OfficeAssignment` de procédures stockées (consultez la après la capture d’écran), puis cliquez sur **Terminer**. (Ces procédures stockées étaient déjà présents dans la base de données lorsque vous téléchargez ou créé à l’aide d’un script.)

[![Image02](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image26.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image25.png)

Cliquez sur le `OfficeAssignment` entité et sélectionnez **mappage de procédure stockée**.

[![Image03](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image28.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image27.png)

Définir le **insérer**, **mise à jour**, et **supprimer** fonctions à utiliser leurs correspondantes des procédures stockées. Pour le `OrigTimestamp` paramètre de la `Update` de fonction, définissez la **propriété** à `Timestamp` et sélectionnez le **utiliser la valeur d’origine** option.

[![Image04](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image30.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image29.png)

Lorsque l’Entity Framework appelle la `UpdateOfficeAssignment` une procédure stockée, elle passe la valeur d’origine de la `Timestamp` colonne dans la `OrigTimestamp` paramètre. La procédure stockée utilise ce paramètre dans son `Where` clause :

[!code-sql[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample8.sql)]

La procédure stockée sélectionne également la nouvelle valeur de la `Timestamp` colonne après la mise à jour afin que l’Entity Framework peut conserver la `OfficeAssignment` entité qui est synchronisée avec la ligne correspondante de la base de données en mémoire.

(Notez que la procédure stockée pour la suppression d’une attribution n’absence un `OrigTimestamp` paramètre. Pour cette raison, Entity Framework ne peut pas vérifier qu’une entité n’est pas modifiée avant de le supprimer.)

Enregistrez et fermez le modèle de données.

### <a name="adding-officeassignment-methods-to-the-dal"></a>Ajout de méthodes OfficeAssignment à la couche DAL

Ouvrez *ISchoolRepository.cs* et ajoutez les méthodes CRUD suivantes pour le `OfficeAssignment` jeu d’entités :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample9.cs)]

Ajouter les nouvelles méthodes suivantes pour *SchoolRepository.cs*. Dans le `UpdateOfficeAssignment` méthode, vous appelez local `SaveChanges` méthode à la place de `context.SaveChanges`.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample10.cs)]

Dans le projet de test, ouvrez *MockSchoolRepository.cs* et ajoutez le code suivant `OfficeAssignment` collection et méthodes CRUD. (Les données de référentiel fictive doivent implémenter l’interface de référentiel, ou la solution ne sera pas compiler.)

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample11.cs)]

### <a name="adding-officeassignment-methods-to-the-bll"></a>Ajout de méthodes OfficeAssignment à la couche BLL

Dans le projet principal, ouvrez *SchoolBL.cs* et ajoutez les méthodes CRUD suivantes pour le `OfficeAssignment` du jeu d’entités à celui-ci :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample12.cs)]

## <a name="creating-an-officeassignments-web-page"></a>Création d’une Page Web de OfficeAssignments

Créer une page web qui utilise le *Site.Master* page maître et nommez-le *OfficeAssignments.aspx*. Ajoutez le balisage suivant à la `Content` contrôle nommé `Content2`:

[!code-aspx[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample13.aspx)]

Notez que dans le `DataKeyNames` d’attribut, le balisage Spécifie le `Timestamp` propriété, ainsi que la clé d’enregistrement (`InstructorID`). Définition des propriétés dans le `DataKeyNames` attribut entraîne le contrôle pour les enregistrer dans l’état du contrôle (qui est similaire à l’état d’affichage) afin que les valeurs d’origine sont disponibles lors du traitement de publication (postback).

Si vous n’avez pas enregistré le `Timestamp` valeur, Entity Framework n’aurait pas pour le `Where` clause de l’instruction SQL `Update` commande. Par conséquent, rien ne se trouve à mettre à jour. Par conséquent, Entity Framework lève une exception d’accès concurrentiel optimiste chaque fois qu’un `OfficeAssignment` mise à jour d’entité.

Ouvrez *OfficeAssignments.aspx.cs* et ajoutez le code suivant `using` instruction pour la couche d’accès aux données :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample14.cs)]

Ajoutez le code suivant `Page_Init` (méthode), ce qui active des fonctionnalités Dynamic Data. Également ajouter le gestionnaire suivant pour le `ObjectDataSource` du contrôle `Updated` événement afin de vérifier les erreurs d’accès concurrentiel :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample15.cs)]

### <a name="testing-optimistic-concurrency-in-the-officeassignments-page"></a>Test de l’accès concurrentiel optimiste dans la Page OfficeAssignments

Exécutez le *OfficeAssignments.aspx* page.

[![Image10](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image32.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image31.png)

Cliquez sur **modifier** dans une ligne et modifiez la valeur dans la **emplacement** colonne.

[![Image11](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image34.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image33.png)

Ouvrez une nouvelle fenêtre de navigateur et exécutez à nouveau la page (copier l’URL à partir de la première fenêtre de navigateur à la deuxième fenêtre de navigateur).

[![Image10](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image36.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image35.png)

Cliquez sur **modifier** dans la même ligne vous modifié précédemment et modifiez le **emplacement** valeur à autre chose.

[![Image12](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image38.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image37.png)

Dans la deuxième fenêtre de navigateur, cliquez sur **mise à jour**.

[![Image13](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image40.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image39.png)

Basculez vers la première fenêtre de navigateur et cliquez sur **mise à jour**.

[![Image15](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image42.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image41.png)

Vous voyez un message d’erreur et le **emplacement** valeur a été mis à jour pour afficher la valeur de modification à dans la deuxième fenêtre de navigateur.

## <a name="handling-concurrency-with-the-entitydatasource-control"></a>Gestion d’accès concurrentiel avec le contrôle EntityDataSource

Le `EntityDataSource` contrôle inclut une logique intégrée qui reconnaît les paramètres d’accès concurrentiel dans le modèle de données et des descripteurs de mettre à jour en conséquence les opérations de suppression. Toutefois, comme avec toutes les exceptions, vous devez gérer `OptimisticConcurrencyException` exceptions vous-même afin de fournir un message d’erreur convivial.

Ensuite, vous allez configurer le *Courses.aspx* page (qui utilise un `EntityDataSource` contrôle) pour autoriser la mise à jour et supprimer des opérations et pour afficher un message d’erreur si un conflit de concurrence se produit. Le `Course` entité n’a pas une concurrence le suivi de colonne, utilisez la même méthode que vous le faisiez avec les `Department` entité : suivre les valeurs de toutes les propriétés non clé.

Ouvrez le *SchoolModel.edmx* fichier. Pour les propriétés non clé de la `Course` entité (`Title`, `Credits`, et `DepartmentID`), définissez la **Mode d’accès concurrentiel** propriété `Fixed`. Ensuite, enregistrez et fermez le modèle de données.

Ouvrez le *Courses.aspx* page et apportez les modifications suivantes :

- Dans le `CoursesEntityDataSource` , ajoutez `EnableUpdate="true"` et `EnableDelete="true"` attributs. La balise d’ouverture de ce contrôle ressemble maintenant à l’exemple suivant :

    [!code-aspx[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample16.aspx)]
- Dans le `CoursesGridView` contrôler, modifier le `DataKeyNames` valeur d’attribut `"CourseID,Title,Credits,DepartmentID"`. Ajoutez ensuite un `CommandField` élément à la `Columns` élément indique **modifier** et **supprimer** boutons (`<asp:CommandField ShowEditButton="True" ShowDeleteButton="True" />`). Le `GridView` contrôle ressemble maintenant à l’exemple suivant :

    [!code-aspx[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample17.aspx)]

Exécuter la page et créer une situation de conflit, comme vous le faisiez avant la page Services. Exécutez la page dans deux fenêtres de navigateur, cliquez sur **modifier** dans la même ligne dans chaque fenêtre et apportez une modification différente dans chacun d’eux. Cliquez sur **mise à jour** dans une fenêtre, puis cliquez sur **mise à jour** dans l’autre fenêtre. Lorsque vous cliquez sur **mise à jour** la deuxième fois, vous voyez la page d’erreur qui résulte d’une exception d’accès concurrentiel non prise en charge.

[![Image22](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image44.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image43.png)

Vous traitez cette erreur de manière très similaire à la façon dont vous avez géré pour le `ObjectDataSource` contrôle. Ouvrir le *Courses.aspx* page, puis, dans le `CoursesEntityDataSource` (contrôle), spécifient des gestionnaires pour le `Deleted` et `Updated` événements. La balise d’ouverture du contrôle ressemble maintenant à l’exemple suivant :

[!code-aspx[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample18.aspx)]

Avant du `CoursesGridView` contrôle, ajoutez le code suivant `ValidationSummary` contrôle :

[!code-aspx[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample19.aspx)]

Dans *Courses.aspx.cs*, ajouter un `using` instruction pour le `System.Data` espace de noms, ajoutez une méthode qui vérifie pour les exceptions d’accès concurrentiel et ajouter des gestionnaires pour les `EntityDataSource` du contrôle `Updated` et `Deleted`gestionnaires. Le code ressemble à ce qui suit :

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample20.cs)]

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/samples/sample21.cs)]

La seule différence entre ce code et que vous l’avez fait le `ObjectDataSource` contrôle est dans ce cas l’exception d’accès concurrentiel dans le `Exception` propriété de l’objet arguments d’événement plutôt que de cette exception `InnerException` propriété.

Exécuter la page et recréez un conflit d’accès concurrentiel. Cette fois, vous voyez un message d’erreur :

[![Image23](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image46.png)](handling-concurrency-with-the-entity-framework-in-an-asp-net-web-application/_static/image45.png)

Cette étape termine l’introduction à la gestion des conflits d’accès concurrentiel. Le didacticiel suivant fournissent des instructions sur la façon d’améliorer les performances dans une application web qui utilise Entity Framework.

>[!div class="step-by-step"]
[Précédent](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering.md)
[Suivant](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application.md)
