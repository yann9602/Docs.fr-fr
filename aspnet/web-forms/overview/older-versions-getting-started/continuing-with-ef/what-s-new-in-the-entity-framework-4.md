---
uid: web-forms/overview/older-versions-getting-started/continuing-with-ef/what-s-new-in-the-entity-framework-4
title: "Quelles sont les nouveautés dans Entity Framework 4.0 | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels s’appuie sur l’application web de l’université Contoso qui est créée par la prise en main de la série de didacticiels Entity Framework 4.0. I..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/26/2011
ms.topic: article
ms.assetid: 393df4a8-b1db-44c4-9db7-2b533ca887d0
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/continuing-with-ef/what-s-new-in-the-entity-framework-4
msc.type: authoredcontent
ms.openlocfilehash: c114627388217e892c84d6b76366d0fa96b0b70c
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="whats-new-in-the-entity-framework-40"></a>Quelles sont les nouveautés dans Entity Framework 4.0
====================
Par [Tom Dykstra](https://github.com/tdykstra)

> Cette série de didacticiels s’appuie sur l’application web de l’université Contoso qui est créée par le [mise en route avec Entity Framework](../getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-1.md) série de didacticiels. Si vous n’avez pas terminé les didacticiels antérieures, comme point de départ pour ce didacticiel vous pouvez [télécharger l’application](https://code.msdn.microsoft.com/ASPNET-Web-Forms-97f8ee9a) que vous devriez avoir créé. Vous pouvez également [télécharger l’application](https://code.msdn.microsoft.com/ASPNET-Web-Forms-6c7197aa) qui est créé par la série de didacticiels terminée. Si vous avez des questions sur les didacticiels, vous pouvez les valider pour le [forum de ASP.NET Entity Framework](https://forums.asp.net/1227.aspx).


Dans le didacticiel précédent, vous avez vu certaines méthodes pour optimiser les performances d’une application web qui utilise Entity Framework. Ce didacticiel passe en revue quelques-unes des plus importantes nouvelles fonctionnalités dans la version 4 d’Entity Framework, et il liens vers des ressources qui fournissent une présentation plus complète pour toutes les nouvelles fonctionnalités. Les fonctionnalités de mise en surbrillance dans ce didacticiel sont les suivantes :

- Associations de clé étrangère.
- L’exécution des commandes SQL définie par l’utilisateur.
- Développement du modèle en premier.
- Prise en charge POCO.

En outre, le didacticiel vous présente brièvement *code-first-développement*, une fonctionnalité qui sera disponible dans la prochaine version d’Entity Framework.

Pour démarrer le didacticiel, démarrez Visual Studio et ouvrez l’application web de Contoso University que vous utilisiez dans le didacticiel précédent.

## <a name="foreign-key-associations"></a>Associations de clé étrangère

Version 3.5 d’Entity Framework inclut des propriétés de navigation, mais il n’a pas d’inclure les propriétés de clé étrangère dans le modèle de données. Par exemple, le `CourseID` et `StudentID` les colonnes de la `StudentGrade` table sont omise de la `StudentGrade` entité.

[![Image01](what-s-new-in-the-entity-framework-4/_static/image2.png)](what-s-new-in-the-entity-framework-4/_static/image1.png)

La raison de cette approche est que, en principe, les clés étrangères sont un détail d’implémentation physique et n’appartenant pas à un modèle conceptuel de données. Toutefois, un point de vue pratique, il est souvent plus facile de travailler avec les entités dans le code lorsque vous disposez d’un accès direct aux clés étrangères.

Pour obtenir un exemple de clés étrangères comment le modèle de données peut simplifier votre code, pensez comment vous deviez code le *DepartmentsAdd.aspx* page sans eux. Dans le `Department` entité, le `Administrator` propriété est une clé étrangère qui correspond à `PersonID` dans le `Person` entité. Afin d’établir l’association entre un service et son administrateur, vous n’aviez faire a été défini la valeur de la `Administrator` propriété dans le `ItemInserting` Gestionnaire d’événements du contrôle lié aux données :

[!code-csharp[Main](what-s-new-in-the-entity-framework-4/samples/sample1.cs)]

Sans les clés étrangères dans le modèle de données, vous pouvez gérer le `Inserting` événement du contrôle de source de données au lieu du `ItemInserting` événement du contrôle lié aux données, afin d’obtenir une référence à l’entité elle-même avant que l’entité est ajoutée au jeu d’entités. Lorsque vous disposez de cette référence, vous établissez l’association à l’aide de code similaire dans les exemples suivants :

[!code-csharp[Main](what-s-new-in-the-entity-framework-4/samples/sample2.cs)]

[!code-csharp[Main](what-s-new-in-the-entity-framework-4/samples/sample3.cs)]

Comme vous pouvez le voir dans l’équipe de Entity Framework [billet de blog sur les associations de clé étrangère](https://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx), il existe des autres cas où la différence de la complexité du code est beaucoup plus importante. Pour répondre aux besoins de ceux qui préfèrent live avec les détails d’implémentation dans le modèle conceptuel de données pour des raisons de code plus simple, Entity Framework permet à présent la possibilité d’inclure des clés étrangères dans le modèle de données.

Dans la terminologie Entity Framework, si vous incluez des clés étrangères dans le modèle de données vous utilisez *des associations de clés étrangères*, et si vous excluez des clés étrangères, vous utilisez *associations indépendantes*.

## <a name="executing-user-defined-sql-commands"></a>L’exécution des commandes SQL définies par l’utilisateur

Dans les versions antérieures d’Entity Framework, il n’existait aucun moyen facile pour créer vos propres commandes SQL à la volée et les exécuter. Entity Framework généré dynamiquement des commandes SQL pour vous, ou vous deviez créer une procédure stockée et l’importer en tant que fonction. La version 4 ajoute `ExecuteStoreQuery` et `ExecuteStoreCommand` méthodes la `ObjectContext` classe qui facilitent la passer n’importe quelle requête directement à la base de données.

Supposons que les administrateurs de Contoso University veulent être en mesure d’effectuer des modifications en bloc dans la base de données sans avoir à passer par le processus de création d’une procédure stockée et les importer dans le modèle de données. Sa première demande est pour une page qui leur permet de modifier le nombre de crédits pour tous les cours dans la base de données. Dans la page web, ils souhaitent pouvoir entrer un nombre à utiliser pour multiplier la valeur de chaque `Course` de ligne `Credits` colonne.

Créer une nouvelle page qui utilise le *Site.Master* page maître et nommez-le *UpdateCredits.aspx*. Puis ajoutez le balisage suivant à la `Content` contrôle nommé `Content2`:

[!code-aspx[Main](what-s-new-in-the-entity-framework-4/samples/sample4.aspx)]

Cette balise crée un `TextBox` contrôle dans lequel l’utilisateur peut entrer la valeur de multiplicateur un `Button` contrôle cliquer afin d’exécuter la commande et un `Label` contrôle pour indiquer le nombre de lignes affectées.

Ouvrez *UpdateCredits.aspx.cs*et ajoutez le code suivant `using` instruction et un gestionnaire pour du bouton `Click` événements :

[!code-csharp[Main](what-s-new-in-the-entity-framework-4/samples/sample5.cs)]

[!code-csharp[Main](what-s-new-in-the-entity-framework-4/samples/sample6.cs)]

Ce code exécute l’instruction SQL `Update` commande à l’aide de la valeur dans la zone de texte et utilise l’étiquette pour afficher le nombre de lignes affectées. Avant d’exécuter la page, exécutez le *Courses.aspx* page pour obtenir une image « avant » de certaines données.

[![Image02](what-s-new-in-the-entity-framework-4/_static/image4.png)](what-s-new-in-the-entity-framework-4/_static/image3.png)

Exécutez *UpdateCredits.aspx*, entrez « 10 » comme multiplicateur, puis cliquez sur **Execute**.

[![Image03](what-s-new-in-the-entity-framework-4/_static/image6.png)](what-s-new-in-the-entity-framework-4/_static/image5.png)

Exécutez le *Courses.aspx* page pour afficher les données modifiées.

[![Image04](what-s-new-in-the-entity-framework-4/_static/image8.png)](what-s-new-in-the-entity-framework-4/_static/image7.png)

(Si vous souhaitez définir le nombre de crédits vers leurs valeurs d’origine, en *UpdateCredits.aspx.cs* modifier `Credits * {0}` à `Credits / {0}` et exécutez de nouveau la page, entrez 10 comme diviseur.)

Pour plus d’informations sur l’exécution de requêtes que vous définissez dans le code, consultez [Comment : directement exécuter des commandes par rapport à la Source de données](https://msdn.microsoft.com/library/ee358769.aspx).

## <a name="model-first-development"></a>Développement basé d’abord de modèle

Dans ces procédures pas à pas vous d’abord créé la base de données et puis généré le modèle de données basé sur la structure de base de données. Dans Entity Framework 4, vous pouvez démarrer avec le modèle de données à la place et générer la base de données basée sur la structure de modèle de données. Si vous créez une application pour laquelle la base de données n’existe pas déjà, le modèle dans l’approche vous permet de créer des entités et des relations qui se justifient sur le plan conceptuel pour l’application, tout ne pas tenir compte des détails d’implémentation physique . (Cela reste vrai uniquement via les premières étapes du développement, cependant. Finit par la base de données sera créée et est des données de production et en le recréant à partir du modèle ne pourra plus être pratique ; à ce stade vous pourrez revenir à l’approche de la base de données en premier.)

Dans cette section du didacticiel, vous allez créer un modèle de données simple et générer la base de données à partir de celui-ci.

Dans **l’Explorateur de solutions**, avec le bouton droit le *DAL* et sélectionnez **ajouter un nouvel élément**. Dans le **ajouter un nouvel élément** boîte de dialogue **modèles installés** sélectionnez **données** , puis sélectionnez le **ADO.NET Entity Data Model** modèle . Nommez le nouveau fichier *AlumniAssociationModel.edmx* et cliquez sur **ajouter**.

[![Image06](what-s-new-in-the-entity-framework-4/_static/image10.png)](what-s-new-in-the-entity-framework-4/_static/image9.png)

Cette opération lance l’Assistant Entity Data Model. Dans le **choisir le contenu du modèle** étape, sélectionnez **modèle vide** puis cliquez sur **Terminer**.

[![Image07](what-s-new-in-the-entity-framework-4/_static/image12.png)](what-s-new-in-the-entity-framework-4/_static/image11.png)

Le **Entity Data Model Designer** s’ouvre avec une aire de conception vide. Faites glisser un **entité** d’élément à partir de la **boîte à outils** sur l’aire de conception.

[![Image08](what-s-new-in-the-entity-framework-4/_static/image14.png)](what-s-new-in-the-entity-framework-4/_static/image13.png)

Modifier le nom de l’entité à partir de `Entity1` à `Alumnus`, modifiez le `Id` nom de propriété à `AlumnusId`et ajouter une nouvelle propriété scalaire nommée `Name`. Pour ajouter de nouvelles propriétés vous appuyez sur ENTRÉE après avoir modifié le nom de la `Id` colonne, ou cliquez sur l’entité et sélectionnez **ajouter une propriété scalaire**. Le type de valeur par défaut pour les nouvelles propriétés est `String`, qui est tout indiquée pour cette démonstration simple, mais bien entendu, vous pouvez modifier les choses comme type de données dans le **propriétés** fenêtre.

Créez une autre entité de la même façon et nommez-le `Donation`. Modifier la `Id` propriété `DonationId` et ajouter une propriété scalaire nommée `DateAndAmount`.

[![Image09](what-s-new-in-the-entity-framework-4/_static/image16.png)](what-s-new-in-the-entity-framework-4/_static/image15.png)

Pour ajouter une association entre ces deux entités, cliquez sur le `Alumnus` entité, sélectionnez **ajouter**, puis sélectionnez **Association**.

[![Image10](what-s-new-in-the-entity-framework-4/_static/image18.png)](what-s-new-in-the-entity-framework-4/_static/image17.png)

Les valeurs par défaut dans le **ajouter une Association** boîte de dialogue que vous êtes (un-à-plusieurs, inclure des propriétés de navigation, inclure des clés étrangères), donc cliquez **OK**.

[![Image11](what-s-new-in-the-entity-framework-4/_static/image20.png)](what-s-new-in-the-entity-framework-4/_static/image19.png)

Le concepteur ajoute une ligne d’association et une propriété de clé étrangère.

[![Image12](what-s-new-in-the-entity-framework-4/_static/image22.png)](what-s-new-in-the-entity-framework-4/_static/image21.png)

Vous êtes maintenant prêt à créer la base de données. Cliquez sur l’aire de conception et sélectionnez **générer la base de données à partir du modèle**.

[![Image13](what-s-new-in-the-entity-framework-4/_static/image24.png)](what-s-new-in-the-entity-framework-4/_static/image23.png)

Cette opération lance l’Assistant génération de la base de données. (Si vous voyez des avertissements qui indiquent que les entités ne sont pas mappées, vous pouvez ignorer ceux pour l’instant.)

Dans le **choisir votre connexion de données** étape, cliquez sur **nouvelle connexion**.

[![Image14](what-s-new-in-the-entity-framework-4/_static/image26.png)](what-s-new-in-the-entity-framework-4/_static/image25.png)

Dans le **propriétés de connexion** boîte de dialogue zone, sélectionnez l’instance locale de SQL Server Express et nom de la base de données `AlumniAsssociation`.

[![Image15](what-s-new-in-the-entity-framework-4/_static/image28.png)](what-s-new-in-the-entity-framework-4/_static/image27.png)

Cliquez sur **Oui** lorsque vous êtes invité si vous souhaitez créer la base de données. Lorsque le **choisir votre connexion de données** étape s’affiche de nouveau, cliquez sur **suivant**.

Dans le **résumé et les paramètres** étape, cliquez sur **Terminer**.

[![Image18](what-s-new-in-the-entity-framework-4/_static/image30.png)](what-s-new-in-the-entity-framework-4/_static/image29.png)

A *.sql* fichier avec les commandes data definition language (DDL) est créé, mais les commandes n’ont pas encore été exécutés.

[![Image20](what-s-new-in-the-entity-framework-4/_static/image32.png)](what-s-new-in-the-entity-framework-4/_static/image31.png)

Utiliser un outil tel que **SQL Server Management Studio** pour exécuter le script et créer les tables que vous avez effectué lorsque vous avez créé le `School` pour la base de données [le premier didacticiel de la série de didacticiels mise en route ](../getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-1.md). (Sauf si vous avez téléchargé la base de données).

Vous pouvez maintenant utiliser le `AlumniAssociation` modèle de données dans votre site web pages de la même façon que vous utilisez la `School` modèle. Pour l’essayer, ajoutez des données aux tables et créer une page web qui affiche les données.

À l’aide de **l’Explorateur de serveurs**, ajoutez les lignes suivantes à la `Alumnus` et `Donation` tables.

[![Image21](what-s-new-in-the-entity-framework-4/_static/image34.png)](what-s-new-in-the-entity-framework-4/_static/image33.png)

Créer une nouvelle page web nommée *Alumni.aspx* qui utilise le *Site.Master* page maître. Ajoutez le balisage suivant à la `Content` contrôle nommé `Content2`:

[!code-aspx[Main](what-s-new-in-the-entity-framework-4/samples/sample7.aspx)]

Cette balise crée imbriquée `GridView` des contrôles, celui externe pour afficher les noms des anciens élèves et celle interne pour afficher les dates de don et quantités.

Ouvrez *Alumni.aspx.cs*. Ajouter un `using` instruction pour les données d’accès couche et un gestionnaire d’expiration `GridView` du contrôle `RowDataBound` événement :

[!code-csharp[Main](what-s-new-in-the-entity-framework-4/samples/sample8.cs)]

Ce code d’établit interne `GridView` contrôler à l’aide la `Donations` propriété de navigation de la ligne actuelle `Alumnus` entité.

Exécutez la page.

[![Image22](what-s-new-in-the-entity-framework-4/_static/image36.png)](what-s-new-in-the-entity-framework-4/_static/image35.png)

(Remarque : cette page est incluse dans le projet téléchargeable, mais pour la rendre opérationnelle, vous devez créer la base de données dans votre instance locale de SQL Server Express ; la base de données n’est pas inclus en tant qu’un *.mdf* de fichiers dans le *application\_ Données* dossier.)

Pour plus d’informations sur l’utilisation de la fonctionnalité de modèle d’Entity Framework, consultez [Model-First dans Entity Framework 4](https://msdn.microsoft.com/data/ff830362.aspx).

## <a name="poco-support"></a>Prise en charge POCO

Lorsque vous utilisez la méthodologie de conception de domaine, vous concevez des classes de données qui représentent les données et le comportement qui s’applique au domaine d’entreprise. Ces classes doivent être indépendants d’une technologie spécifique utilisée pour stocker (conserver) les données ; en d’autres termes, ils doivent être *ignorant la persistance*. Ignorant la persistance peut également faciliter une classe de test unitaire car le projet de test unitaire peut utiliser toute technologie de persistance est plus pratique pour le test. Les versions antérieures d’Entity Framework proposé ignorant la persistance d’une prise en charge limitée, car les classes d’entité hérite les `EntityObject` classe et donc inclus un grand nombre de fonctionnalités spécifiques à Entity Framework.

L’Entity Framework 4 introduit la possibilité d’utiliser les classes d’entité qui n’héritent pas de la `EntityObject` classe et ne sont donc persistance ignorant. Dans le cadre d’Entity Framework, les classes, comme ce sont généralement appelées *objets de brut-old CLR* (POCO ou POCOs). Vous pouvez écrire des classes POCO manuellement, ou vous pouvez générer automatiquement en fonction d’un modèle de données existant à l’aide de modèles de Text Template Transformation Toolkit (T4) fournis par Entity Framework.

Pour plus d’informations sur l’utilisation de POCOs dans Entity Framework, consultez les ressources suivantes :

- [Utilisation des entités POCO](https://msdn.microsoft.com/library/dd456853.aspx). Il s’agit d’un document MSDN qui est une vue d’ensemble de POCOs, avec des liens vers d’autres documents qui possèdent des informations plus détaillées.
- [Procédure pas à pas : POCO modèle pour Entity Framework](https://blogs.msdn.com/b/adonet/archive/2010/01/25/walkthrough-poco-template-for-the-entity-framework.aspx) il s’agit d’un billet de blog de l’équipe de développement Entity Framework, avec des liens vers d’autres billets de blog sur POCOs.

## <a name="code-first-development"></a>Développement de code en premier.

Prise en charge POCO dans Entity Framework 4 nécessite toujours que vous créez un modèle de données et la liaison de vos classes d’entité au modèle de données. La prochaine version d’Entity Framework inclut une fonctionnalité appelée *code-first-développement*. Cette fonctionnalité vous permet d’utiliser Entity Framework avec vos propres classes POCO sans avoir besoin d’utiliser le Générateur de modèles de données ou un fichier XML de modèle de données. (Par conséquent, cette option a également été appelée *code uniquement*; *code-first-* et *code uniquement* se rapportent à la même fonctionnalité Entity Framework.)

Pour plus d’informations sur l’utilisation de l’approche code-first-développement, consultez les ressources suivantes :

- [Code-First-développement avec Entity Framework 4](https://weblogs.asp.net/scottgu/archive/2010/07/16/code-first-development-with-entity-framework-4.aspx). Il s’agit d’un billet de blog de développement code-first-présentation de Scott Guthrie.
- [Billets de Blog d’équipe développement Entity Framework - de CodeOnly avec balises](https://blogs.msdn.com/b/efdesign/archive/tags/codeonly/)
- [Billets de Blog d’équipe développement Entity Framework - de Code First avec balises](https://blogs.msdn.com/b/efdesign/archive/tags/code+first/)
- [Didacticiel de magasin de musique MVC - partie 4 : accès aux données et modèles](../../../../mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-4.md)
- [Mise en route avec MVC 3 - partie 4 : développement de Code-First de Entity Framework](../../../../mvc/overview/older-versions/getting-started-with-aspnet-mvc3/cs/adding-a-model.md)

En outre, un nouveau didacticiel MVC Code-First qui génère une application similaire à l’application Contoso University est prévu pour être publié dans le ressort de 2011 à [https://asp.net/entity-framework/tutorials](../../../../entity-framework.md)

## <a name="more-information"></a>Informations complémentaires

Cette étape termine la vue d’ensemble à ce qui est nouvelle dans Entity Framework et de poursuite de l’opération avec la série de didacticiels Entity Framework. Pour plus d’informations sur les nouvelles fonctionnalités qui ne sont pas couverts ici dans Entity Framework 4, consultez les ressources suivantes :

- [Nouveautés dans ADO.NET](https://msdn.microsoft.com/library/ex6y04yf.aspx) rubrique MSDN sur les nouvelles fonctionnalités dans la version 4 d’Entity Framework.
- [Annonce de la version d’Entity Framework 4](https://blogs.msdn.com/b/efdesign/archive/2010/04/12/announcing-the-release-of-entity-framework-4.aspx) billet de blog de l’équipe de développement Entity Framework sur les nouvelles fonctionnalités dans la version 4.

>[!div class="step-by-step"]
[Précédent](maximizing-performance-with-the-entity-framework-in-an-asp-net-web-application.md)
