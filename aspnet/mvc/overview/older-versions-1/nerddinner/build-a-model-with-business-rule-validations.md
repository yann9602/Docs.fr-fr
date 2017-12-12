---
uid: mvc/overview/older-versions-1/nerddinner/build-a-model-with-business-rule-validations
title: "Générer un modèle avec les Validations de règle d’entreprise | Documents Microsoft"
author: microsoft
description: "Étape 3 montre comment créer un modèle que nous pouvons utiliser pour les deux requêtes et mettre à jour la base de données pour notre application NerdDinner."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: 0bc191b2-4311-479a-a83a-7f1b1c32e6fe
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/build-a-model-with-business-rule-validations
msc.type: authoredcontent
ms.openlocfilehash: dbe6370979f218988c168df3e80314ef9b338fbd
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="build-a-model-with-business-rule-validations"></a>Générer un modèle avec les Validations de règle d’entreprise
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> Il s’agit d’étape 3 a gratuit [« « l’application NerdDinner](introducing-the-nerddinner-tutorial.md) qui parcours à comment générer un petit, mais se termine, application web à l’aide d’ASP.NET MVC 1.
> 
> Étape 3 montre comment créer un modèle que nous pouvons utiliser pour les deux requêtes et mettre à jour la base de données pour notre application NerdDinner.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-step-3-building-the-model"></a>NerdDinner étape 3 : Création du modèle

Dans une infrastructure model-view-controller le terme « modèle » fait référence aux objets qui représentent les données de l’application, ainsi que la logique de domaine correspondante qui intègre la validation et les règles d’entreprise avec lui. Le modèle est bien des égards « cœur » d’une application MVC et comme nous le verrons plus tard fondamentalement lecteurs le comportement de celui-ci.

L’infrastructure ASP.NET MVC prend en charge à l’aide d’une technologie d’accès aux données et les développeurs peuvent choisir à partir d’une variété d’options de données .NET riches pour implémenter les modèles, y compris : LINQ to Entities, LINQ to SQL, NHibernate, LLBLGen Pro, SubSonic, WilsonORM ou ADO brute. NET DataReaders ou jeux de données.

Pour notre application NerdDinner, nous allons utiliser LINQ to SQL pour créer un modèle simple qui correspond plutôt à notre conception de base de données et ajoute des règles d’entreprise et la logique de validation personnalisée. Nous sera ensuite implémenter une classe de référentiel qui vous aide à abstraite immédiatement l’implémentation de persistance des données du reste de l’application et vous permet de nous unité facilement le tester.

### <a name="linq-to-sql"></a>LINQ to SQL

LINQ to SQL est un ORM (mappeur objet / relationnel) qui est fourni dans le cadre de .NET 3.5.

LINQ to SQL fournit un moyen simple pour mapper des tables de base de données pour les classes .NET que nous pouvons coder. Pour notre application NerdDinner nous allons l’utiliser pour mapper les tables préparés et RSVP dans notre base de données pour les classes Dinner et RSVP. Les colonnes des tables préparés et RSVP correspond aux propriétés sur les classes Dinner et RSVP. Chaque objet dîner et RSVP représente une ligne distincte dans les tables préparés ou RSVP dans la base de données.

LINQ to SQL permet d’éviter d’avoir à construire des instructions SQL pour récupérer et mettre à jour dîner et RSVP manuellement les objets de base de données. Au lieu de cela, nous définirons les classes Dinner et RSVP, comment elles sont mappées vers/à partir de la base de données et les relations entre eux. LINQ to SQL sera prend ensuite le soin de génération de la logique d’exécution SQL appropriée à utiliser lors de l’exécution, lorsque nous interagissent et que vous les utilisez.

Nous pouvons utiliser la prise en charge du langage LINQ dans Visual Basic et c# pour écrire des requêtes expressives qui récupèrent dîner et RSVP objets à partir de la base de données. Cela réduit la quantité de code de données que nous avons besoin d’écrire, et nous permet de créer des applications vraiment nettoyer.

### <a name="adding-linq-to-sql-classes-to-our-project"></a>Ajout Classes LINQ to SQL à notre projet

Nous allons commencer en cliquant sur le dossier « Modèles » dans notre projet, puis sélectionnez le **Add -&gt;un nouvel élément** commande de menu :

![](build-a-model-with-business-rule-validations/_static/image1.png)

La boîte de dialogue « Ajouter un nouvel élément » s’affiche. Nous allons filtrer par la catégorie « Données » et sélectionnez le modèle de « Classes de LINQ à SQL » qu’il contient :

![](build-a-model-with-business-rule-validations/_static/image2.png)

Nous allons nommez l’élément « NerdDinner » et cliquez sur le bouton « Ajouter ». Visual Studio ajoute un fichier NerdDinner.dbml sous le répertoire de notre \Models et puis ouvrez le concepteur LINQ to SQL objet relationnel :

![](build-a-model-with-business-rule-validations/_static/image3.png)

### <a name="creating-data-model-classes-with-linq-to-sql"></a>Création Classes de modèle de données avec LINQ to SQL

LINQ to SQL vous permet de créer rapidement des classes de modèle de données à partir d’un schéma de base de données existant. Action cela nous allons ouvrir la base de données de NerdDinner dans l’Explorateur de serveurs et sélectionnez les Tables que vous souhaitez de modèle qu’il contient :

![](build-a-model-with-business-rule-validations/_static/image4.png)

Ensuite, nous pouvons faire glisser les tables sur LINQ à l’aire du concepteur SQL. Quand nous faisons ce LINQ to SQL crée automatiquement dîner et classes RSVP en utilisant le schéma des tables (avec les propriétés de la classe qui mappent aux colonnes de table de base de données) :

![](build-a-model-with-business-rule-validations/_static/image5.png)

Par défaut LINQ to SQL designer Pluralise automatiquement » le « noms de table et de colonne lorsqu’il crée des classes basées sur un schéma de base de données. Par exemple : la table « Préparés » dans notre exemple ci-dessus a entraîné une classe « Dîner ». Cette classe d’affectation de noms permet de rendre nos modèles cohérent avec les conventions d’affectation de noms .NET et généralement trouver celui dont la correction du concepteur cette configuration pratique (en particulier lorsque l’ajout d’un grand nombre de tables). Si vous n’aimez pas le nom d’une classe ou une propriété généré par le concepteur, cependant, vous pouvez toujours remplacer et remplacez-le par le nom de que votre choix. Faire cela en modifiant la propriété d’entité nom en ligne dans le concepteur ou en la modifiant par la grille des propriétés.

Par défaut, le concepteur LINQ to SQL également inspecte les relations de clé étrangère/clé primaires des tables et selon automatiquement crée par défaut « associations relation » entre les classes de modèle différent qu'il crée. Par exemple, lorsque nous avons déplacé les préparés et RSVP tables sur le concepteur LINQ to SQL, une association de la relation un-à-plusieurs entre les deux a été inférée basée sur le fait que la table RSVP a une clé étrangère à la table préparés (cela est indiqué par la flèche dans le Concepteur) :

![](build-a-model-with-business-rule-validations/_static/image6.png)

L’association ci-dessus entraîne LINQ to SQL pour ajouter une propriété fortement typée de « Dîner » à la classe RSVP qui permet aux développeurs d’accéder le dîner associé à un RSVP donné. Cela entraînera également la classe dîner d’avoir une propriété de collection « RSVPs » qui permet aux développeurs de récupérer et mettre à jour les objets RSVP associés à un dîner particulier.

Ci-dessous, vous pouvez voir un exemple d’intellisense dans Visual Studio lorsque nous créer un nouvel objet RSVP et l’ajouter à la collection de RSVP d’un repas. Notez comment LINQ to SQL ajouté automatiquement une collection de « RSVP » sur l’objet Dinner :

![](build-a-model-with-business-rule-validations/_static/image7.png)

En ajoutant l’objet RSVP pour collection de RSVP du dîner, nous indiquons LINQ to SQL à associer une relation de clé étrangère entre le dîner et de la ligne RSVP dans notre base de données :

![](build-a-model-with-business-rule-validations/_static/image8.png)

Si vous n’aimez pas comment le concepteur a modélisée ou nommée d’une association de table, vous pouvez le remplacer. Cliquez simplement sur la flèche de l’association dans le concepteur et accéder à ses propriétés via la grille des propriétés pour renommer, supprimer ou modifier. Pour notre application NerdDinner, cependant, les règles d’association par défaut fonctionnent bien pour les classes de modèle de données que nous créons et nous pouvons utiliser simplement le comportement par défaut.

### <a name="nerddinnerdatacontext-class"></a>Classe de NerdDinnerDataContext

Visual Studio crée automatiquement les classes .NET qui représentent les modèles et les relations de base de données définies à l’aide du concepteur LINQ to SQL. Une classe LINQ to SQL DataContext est également généré pour chaque fichier LINQ to SQL concepteur ajouté à la solution. Étant donné que nous avons nommé notre LINQ à l’élément de classe SQL « NerdDinner », la classe DataContext créée sera appelée « NerdDinnerDataContext ». Cette classe NerdDinnerDataContext est le principal moyen qu'interagit avec la base de données.

Notre classe NerdDinnerDataContext expose deux propriétés - « Préparés » et « RSVPs » - qui représentent les deux tables, que nous FAÇONNÉES dans la base de données. Nous pouvons utiliser c# pour écrire des requêtes LINQ sur les propriétés de requête et récupérer des objets dîner et RSVP à partir de la base de données.

Le code suivant montre comment instancier un objet NerdDinnerDataContext et exécuter des requêtes LINQ sur pour obtenir une séquence de préparés qui se produisent dans le futur. Visual Studio fournit intellisense complète lors de l’écriture de la requête LINQ, et les objets retournés à partir de celui-ci sont fortement typées et prennent également en charge intellisense :

![](build-a-model-with-business-rule-validations/_static/image9.png)

En plus de ce qui nous permet d’interroger des objets dîner et RSVP, un NerdDinnerDataContext suit également automatiquement toutes les modifications apportées par la suite aux objets dîner et RSVP que nous extraire par son biais. Nous pouvons utiliser cette fonctionnalité pour enregistrer facilement les modifications à la base de données - sans avoir à écrire de code de mise à jour SQL explicite.

Par exemple, le code ci-dessous montre comment utiliser une requête LINQ pour récupérer un seul objet dîner à partir de la base de données, mettre à jour deux des propriétés dîner, puis enregistrez les modifications apportées à la base de données :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample1.cs)]

L’objet NerdDinnerDataContext dans le code ci-dessus suivies automatiquement les modifications de propriété apportées à l’objet Dinner que nous récupérées à partir de celui-ci. Lorsque nous avons appelé la méthode « SubmitChanges() », il exécutera une instruction « UPDATE » SQL appropriée pour la base de données pour conserver les valeurs de mise à jour.

### <a name="creating-a-dinnerrepository-class"></a>Création d’une classe DinnerRepository

Pour les petites applications, il est parfois disposer de contrôleurs de travailler directement dans une classe LINQ to SQL DataContext et incorporer des requêtes LINQ dans les contrôleurs. Applications deviennent plus importantes, cependant, cette approche devient difficile à maintenir et à tester. Il peut également se produire nous dupliquer les mêmes requêtes LINQ dans plusieurs emplacements.

Que vous pouvez rendre plus facile à gérer et tester les applications consiste à utiliser un modèle de « repository ». Une classe de référentiel permet d’encapsuler des données interrogation et logique de persistance et des résumés immédiatement les détails d’implémentation de la persistance des données à partir de l’application. En plus de rendre le code d’application plus claire, à l’aide d’un modèle de référentiel peut rendre plus facile de modifier des implémentations de stockage de données à l’avenir, et faciliter unité tester une application sans nécessiter une base de données réel.

Pour notre application NerdDinner, nous allons définir une classe de DinnerRepository avec le dessous de signature :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample2.cs)]

*Remarque : Plus loin dans ce chapitre nous extraire une interface IDinnerRepository à partir de cette classe et activer l’injection de dépendance avec lui sur nos contrôleurs. Pour commencer, cependant, nous allons commencer simple et simplement travailler directement avec la classe DinnerRepository.*

Pour implémenter cette classe, nous allons avec le bouton droit sur le dossier « Modèles » et choisissez le **Add -&gt;un nouvel élément** commande de menu. Dans la boîte de dialogue « Ajouter un nouvel élément », nous allons sélectionner le modèle « Classe » et nommez le fichier « DinnerRepository.cs » :

![](build-a-model-with-business-rule-validations/_static/image10.png)

Nous pouvons ensuite implémenter notre classe DinnerRespository en utilisant le code ci-dessous :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample3.cs)]

### <a name="retrieving-updating-inserting-and-deleting-using-the-dinnerrepository-class"></a>La récupération, de mise à jour, d’insertion et de suppression à l’aide de la classe DinnerRepository

Maintenant que nous avons créé notre classe DinnerRepository, examinons quelques exemples de code qui illustrent des tâches courantes, que nous pouvons faire :

#### <a name="querying-examples"></a>Exemples de requêtes

Le code ci-dessous récupère un dîner unique à l’aide de la valeur DinnerID :


[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample4.cs)]

Le code suivant récupère tous les préparés à venir et des boucles au-dessus d’eux :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample5.cs)]

#### <a name="insert-and-update-examples"></a>Insertion et mise à jour

Le code ci-dessous illustre l’ajout de deux préparés de nouveau. Ajouts ou modifications dans le référentiel ne sont pas validées dans la base de données jusqu'à ce que la méthode « Save() » est appelée sur celui-ci. LINQ to SQL encapsule automatiquement toutes les modifications dans une transaction de base de données : soit toutes les modifications se produisent, ou aucun d’eux ne quand notre référentiel enregistre :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample6.cs)]

Le code suivant récupère un objet existant de Dinner et modifie les deux propriétés dessus. Les modifications sont validées dans la base de données lorsque la méthode « Save() » est appelée sur notre référentiel :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample7.cs)]

Le code ci-dessous récupère un dîner et lui ajoute ensuite une réponse. Il procède à l’aide de la collection RSVP sur l’objet de dîner LINQ to SQL créées pour nous (car il existe une relation de clé de clé primaire/étrangère entre les deux dans la base de données). Cette modification est conservée à la base de données en tant qu’une nouvelle ligne de table RSVP lorsque la méthode « Save() » est appelée sur le référentiel :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample8.cs)]

#### <a name="delete-example"></a>Exemple de suppression

Le code ci-dessous récupère un objet Dinner existant et le marque doit être supprimé. Lorsque la méthode « Save() » est appelée sur le référentiel il valide la suppression à la base de données :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample9.cs)]

### <a name="integrating-validation-and-business-rule-logic-with-model-classes"></a>Intégration avec les Classes de modèle de Validation et une logique métier

Intégration de validation et la règle d’entreprise logique est une partie essentielle de toute application qui fonctionne avec les données.

#### <a name="schema-validation"></a>Validation de schéma

Lorsque les classes de modèle sont définies à l’aide du concepteur LINQ to SQL, les types de données des propriétés dans les classes de modèle de données correspondent pour les types de données de la table de base de données. Par exemple : si la colonne de date « événement » dans la table préparés est « datetime », la classe de modèle de données créée par LINQ to SQL sera de type « DateTime » (qui est un type de données .NET intégré). Cela signifie que vous obtenez des erreurs de compilation si vous tentez un entier ou une valeur booléenne lui assigner à partir du code, et il génère une erreur automatiquement si vous essayez de convertir implicitement un type de chaîne non valide à ce dernier lors de l’exécution.

LINQ to SQL sera gère également automatiquement l’échappement de valeurs SQL pour vous lors de l’utilisation de chaînes - qui vous aide à vous protéger contre les attaques par injection SQL lors de son utilisation.

#### <a name="validation-and-business-rule-logic"></a>Validation et une logique métier

Validation de schéma est utile dans un premier temps, mais elle est rarement suffisante. La plupart des scénarios réels requièrent la capacité de spécifier la logique de validation plus riche qui s’étendent sur plusieurs propriétés, d’exécuter du code et ont souvent la reconnaissance de l’état d’un modèle (par exemple : est il en cours de création/mise à jour/supprimées, ou dans un état spécifique à un domaine tels que « archivés »). Il existe une multitude de différents modèles et des infrastructures qui peuvent être utilisés pour définir et appliquer des règles de validation aux classes de modèle, et plusieurs basée sur .NET infrastructures très utiles qui peut être utilisé pour faciliter cette tâche. Vous pouvez utiliser à peu près un d'entre eux dans les applications ASP.NET MVC.

Dans le cadre de notre application NerdDinner, nous utiliserons un modèle relativement simple et directe où nous exposent une propriété IsValid et une méthode GetRuleViolations() sur notre objet de modèle dîner. La propriété IsValid retourne true ou false selon si les règles d’entreprise et de validation sont tous valides. La méthode GetRuleViolations() renvoie une liste de toutes les erreurs de règle.

Nous allons implémenter IsValid et GetRuleViolations() pour notre modèle de dîner en ajoutant une « classe partielle » à notre projet. Classes partielles peuvent être utilisés pour ajouter des propriétés/méthodes/événements aux classes gérées par un concepteur Visual Studio (par exemple, la classe Dinner généré par le concepteur LINQ to SQL) et permettent d’éviter l’outil à partir de notre code travers. Nous pouvons ajouter une nouvelle classe partielle à notre projet en cliquant sur le dossier \Models, puis sélectionnez la commande de menu « Ajouter un nouvel élément ». Nous pouvons puis choisissez le modèle « Classe » dans la boîte de dialogue « Ajouter un nouvel élément » et nommez-le Dinner.cs.

![](build-a-model-with-business-rule-validations/_static/image11.png)

En cliquant sur le bouton « Ajouter » pour ajouter un fichier Dinner.cs à notre projet et ouvrez-le dans l’IDE. Nous pouvons ensuite implémenter un framework de mise en œuvre de règle/validation de base à l’aide du code ci-dessous :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample10.cs)]

Quelques remarques sur le code ci-dessus :

- La classe dîner est précédée d’un mot clé « partiels », ce qui signifie que le code qu’il contient est associé à la classe générée/conservés par le concepteur LINQ to SQL et compilé en une classe unique.
- La classe RuleViolation est une classe d’assistance que nous allons ajouter au projet qui nous permet de fournir plus de détails sur une violation de règle.
- La méthode Dinner.GetRuleViolations() provoque nos règles de validation et d’entreprise à évaluer (nous allons implémenter les peu de temps). Elle retourne ensuite dans une séquence d’objets RuleViolation qui fournissent plus de détails sur les erreurs de règle.
- La propriété Dinner.IsValid fournit une propriété d’assistance pratiques qui indique si l’objet dîner a tout RuleViolations actives. Il peut être vérifiée de manière proactive par un développeur à l’aide de l’objet dîner à tout moment (et ne lève pas d’exception).
- La méthode partielle Dinner.OnValidate() est un raccordement par LINQ to SQL qui permet d’être averti chaque fois que l’objet dîner est sur le point d’être rendues persistantes dans la base de données. Notre implémentation OnValidate() ci-dessus garantit le dîner ne qu’aucune RuleViolations avant d’être enregistré. Si elle est dans un état non valide, il déclenche une exception, ce qui provoque le LINQ to SQL pour abandonner la transaction.

Cette approche fournit une infrastructure simple que nous pouvons intégrer la validation et les règles d’entreprise. Pour l’instant, vous allez ajouter les règles à notre méthode GetRuleViolations() ci-dessous :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample11.cs)]

Nous utilisons la fonction « yield return » de c# pour retourner une séquence de n’importe quel RuleViolations. La première règle de six de contrôle ci-dessus applique simplement que les propriétés de chaîne sur notre dîner ne peut pas être null ou vide. La dernière règle est un peu plus intéressante et appelle une méthode d’assistance de PhoneValidator.IsValidNumber() que nous pouvons ajouter à notre projet pour vérifier que le ContactPhone numéro pays de format correspond à la dîner.

Nous pouvons utiliser. Prise en charge des expressions régulières de NET pour implémenter cette prise en charge de la validation de téléphone. Voici une implémentation simple PhoneValidator que nous pouvons ajouter à notre projet qui permet d’ajouter des vérifications de modèle d’expression régulière spécifique au pays :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample12.cs)]

#### <a name="handling-validation-and-business-logic-violations"></a>Validation de la gestion et les Violations de logique métier

Maintenant que nous avons ajouté le code de règle de validation et entreprise ci-dessus, dès que nous essayons de créer ou mettre à jour un dîner, nos règles de logique de validation seront être évaluées et appliquées.

Les développeurs peuvent écrire de code semblable au suivant pour déterminer de manière proactive si un objet dîner est valide et récupérer une liste de toutes les violations qu’il contient sans lever d’exception :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample13.cs)]

Si vous essayez d’enregistrer un dîner dans un état non valide, une exception sera levée lorsque nous appeler la méthode Save() sur le DinnerRepository. Cela se produit parce que LINQ to SQL appelle automatiquement la méthode partielle Dinner.OnValidate() avant elle enregistre les modifications de la Dinner, nous avons ajouté du code à Dinner.OnValidate() pour lever une exception si des violations de règle existent dans le dîner. Nous pouvons intercepter cette exception et réactive récupérer une liste de corriger les violations de :

[!code-csharp[Main](build-a-model-with-business-rule-validations/samples/sample14.cs)]

Car nos règles de validation et d’entreprise sont implémentés au sein de la couche du modèle et non dans la couche d’interface utilisateur, ils seront appliqués et utilisés pour tous les scénarios dans notre application. Nous pouvons ultérieurement modifier ou ajouter des règles d’entreprise et avoir tout le code que fonctionne avec nos objets Dinner honorer les.

Ayant la possibilité de modifier des règles d’entreprise dans un seul endroit, sans avoir à ces modifications ripple tout au long de l’application et la logique de l’interface utilisateur, est un signe d’une application bien écrite et une offre une infrastructure MVC vous aide à encourager.

### <a name="next-step"></a>Étape suivante

Nous avons maintenant un modèle que nous pouvons utiliser pour interroger et de mettre à jour notre base de données.

Vous allez maintenant ajouter certains contrôleurs et les vues au projet que nous pouvons utiliser pour générer une expérience de l’interface utilisateur HTML autour de lui.

>[!div class="step-by-step"]
[Précédent](create-a-database.md)
[Suivant](use-controllers-and-views-to-implement-a-listingdetails-ui.md)
