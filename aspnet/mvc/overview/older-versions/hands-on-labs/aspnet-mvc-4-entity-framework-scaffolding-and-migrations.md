---
uid: mvc/overview/older-versions/hands-on-labs/aspnet-mvc-4-entity-framework-scaffolding-and-migrations
title: "Génération de modèles automatique ASP.NET MVC 4 Entity Framework et les Migrations | Documents Microsoft"
author: rick-anderson
description: "Si vous êtes familiarisé avec les méthodes de contrôleur ASP.NET MVC 4, ou s’est terminé le &quot;programmes d’assistance, de formulaires et de Validation&quot; atelier pratique, vous devez être conscient..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/18/2013
ms.topic: article
ms.assetid: 093c1362-f10b-407c-a708-be370f4b62b0
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/hands-on-labs/aspnet-mvc-4-entity-framework-scaffolding-and-migrations
msc.type: authoredcontent
ms.openlocfilehash: 15db1589eb90739458b430c35cea38e93e3dec5a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-mvc-4-entity-framework-scaffolding-and-migrations"></a>Migrations et la génération de modèles automatique ASP.NET MVC 4 Entity Framework
====================
par [Web Camps équipe](https://twitter.com/webcamps)

> Si vous êtes familiarisé avec les méthodes de contrôleur ASP.NET MVC 4, ou s’est terminé le &quot;programmes d’assistance, de formulaires et de Validation&quot; atelier pratique, vous devez être conscient que la plupart de la logique permettant de créer, mettre à jour, afficher et supprimer une entité de données qu’il est répété entre l’application. Ne pas de mentionner que, si votre modèle comporte plusieurs classes à manipuler, vous serez susceptible de prendre une écriture les méthodes d’action POST et GET pour chaque opération de l’entité, ainsi que chacune des vues.
> 
> Dans cet atelier, vous allez apprendre à utiliser la génération de modèles automatique ASP.NET MVC 4 pour générer automatiquement la ligne de base CRUD de votre application (création, lecture, mise à jour et suppression). À partir d’une classe de modèle simple et, sans écrire une seule ligne de code, vous allez créer un contrôleur contenant toutes les opérations CRUD, ainsi que toutes les vues nécessaires. Après la génération et exécution de la solution simple, vous devez la base de données d’application généré, ainsi que la logique MVC et les vues pour la manipulation de données.
> 
> En outre, vous allez apprendre combien il est facile à utiliser Entity Framework Migrations pour effectuer des mises à jour dans toute votre application. Entity Framework Migrations vous permettent de modifier votre base de données une fois que le modèle a changé étapes simples. Avec tous ces éléments à l’esprit, vous serez en mesure de créer et gérer des applications web plus efficacement, tirant parti des fonctionnalités plus récentes d’ASP.NET MVC 4.


<a id="Objectives"></a>

<a id="Objectives"></a>
### <a name="objectives"></a>Objectifs

Dans cet atelier pratique, vous allez apprendre comment :

- Utiliser génération de modèles automatique ASP.NET pour les opérations CRUD dans les contrôleurs.
- Modifier le modèle de base de données à l’aide d’Entity Framework Migrations.

<a id="Prerequisites"></a>

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Conditions préalables

Vous devez disposer des éléments suivants pour effectuer ce laboratoire :

- [Microsoft Visual Studio Express 2012 pour Web](https://www.microsoft.com/visualstudio/eng/products/visual-studio-express-for-web) ou supérieure (lecture [annexe A](#AppendixA) pour obtenir des instructions sur l’installation).

<a id="Setup"></a>

<a id="Setup"></a>
### <a name="setup"></a>Installation

**L’installation des extraits de Code**

Pour plus de commodité, une grande partie du code que vous gérez le long de cet atelier est disponible sous les extraits de code Visual Studio. Pour installer les extraits de code exécuter **.\Source\Setup\CodeSnippets.vsi** fichier.

Si vous n’êtes pas familiarisé avec les extraits de Code Visual Studio et vous souhaitez savoir comment les utiliser, vous pouvez faire référence à l’annexe de ce document &quot; [extraits de Code à l’aide de l’annexe b :](#AppendixB)&quot;.

* * *

<a id="Exercises"></a>

<a id="Exercises"></a>
## <a name="exercises"></a>Exercices

L’exercice suivant composent cet atelier pratique :

1. [À l’aide de la génération de modèles automatique ASP.NET MVC 4 avec Entity Framework Migrations](#Exercise1)

> [!NOTE]
> Cet exercice est accompagné par un **fin** dossier qui contient la solution résultante, vous devez obtenir la fin de l’exercice. Si vous avez besoin d’aide supplémentaire sur l’utilisation dans l’exercice, vous pouvez utiliser cette solution comme guide.


Durée estimée pour effectuer ce laboratoire : **30 minutes**

<a id="Exercise1"></a>

<a id="Exercise_1_Using_ASPNET_MVC_4_Scaffolding_with_Entity_Framework_Migrations"></a>
### <a name="exercise-1-using-aspnet-mvc-4-scaffolding-with-entity-framework-migrations"></a>Exercice 1 : À l’aide de la structure d’ASP.NET MVC 4 avec Entity Framework Migrations

Génération de modèles automatique ASP.NET MVC fournit un moyen rapide pour générer les opérations CRUD de façon standardisée, création de la logique nécessaire qui permet à votre application d’interagir avec la couche de base de données.

Dans cet exercice, vous allez apprendre à utiliser la génération de modèles automatique ASP.NET MVC 4 avec le code tout d’abord créer les méthodes CRUD. Ensuite, vous allez apprendre comment mettre à jour votre modèle d’application des modifications dans la base de données à l’aide d’Entity Framework Migrations.

<a id="Ex1Task1"></a>

<a id="Task_1-_Creating_a_new_ASPNET_MVC_4_project_using_Scaffolding"></a>
#### <a name="task-1--creating-a-new-aspnet-mvc-4-project-using-scaffolding"></a>Tâche 1-Création un ASP.NET MVC 4 nouveau projet à l’aide de la génération de modèles automatique

1. S’il est déjà ouvert, démarrez **Visual Studio 2012**.
2. Sélectionnez **fichier | Nouveau projet**. Dans le nouveau projet de boîte de dialogue, sous le **Visual C# | Web** section, sélectionnez **ASP.NET MVC 4 Web Application**. Nommez le projet à **MVC4andEFMigrations** et définissez l’emplacement de **Source\Ex1-UsingMVC4ScaffoldingEFMigrations** dossier de ce laboratoire. Définir le **nom de la Solution** à **commencer** et assurez-vous que **créer le répertoire de solution** est activée. Cliquez sur **OK**.

    ![Nouvelle boîte de dialogue projet ASP.NET MVC 4](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image1.png "nouvelle boîte de dialogue projet ASP.NET MVC 4")

    *Nouvelle boîte de dialogue projet ASP.NET MVC 4*
3. Dans le **nouveau projet ASP.NET MVC 4** boîte de dialogue Sélectionnez le **Application Internet** modèle et vous assurer que **Razor** est sélectionné **moteur d’affichage**. Cliquez sur **OK** pour créer le projet.

    ![Nouvelle Application de Internet ASP.NET MVC 4](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image2.png "nouvelle Application de Internet ASP.NET MVC 4")

    *Nouvelle Application de Internet ASP.NET MVC 4*
4. Dans l’Explorateur de solutions, cliquez sur **modèles** et sélectionnez **ajouter | Classe** pour créer une personne de classe simple (POCO). Nommez-le **personne** et cliquez sur **OK**.
5. Ouvrez la classe de personne et insérer les propriétés suivantes.

    (Code d’extrait de code - *ASP.NET MVC 4 et Entity Framework Migrations - propriétés de personne Ex1*)


    [!code-csharp[Main](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/samples/sample1.cs)]
6. Cliquez sur **générer | Générez la Solution** pour enregistrer les modifications et générez le projet.

    ![Génération de l’application](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image3.png "génération de l’application")

    *Génération de l’Application*
7. Dans l’Explorateur de solutions, cliquez sur le dossier controllers, puis sélectionnez **ajouter | Contrôleur**.
8. Nommez le contrôleur *PersonController* et terminer le **les options de génération de modèles automatique** avec les valeurs suivantes.

    1. Dans le **modèle** la liste déroulante, sélectionnez le **contrôleur MVC avec des actions de lecture/écriture et de vues, utilisant Entity Framework** option.
    2. Dans le **classe de modèle** la liste déroulante, sélectionnez le **personne** classe.
    3. Dans le **classe du contexte de données** liste, sélectionnez  **&lt;nouveau contexte de données... &gt;**. Choisir un nom et cliquez sur **OK**.
    4. Dans le **vues** déroulante liste, assurez-vous que **Razor** est sélectionnée.

    ![Ajout du contrôleur de personne avec la structure](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image4.png "Ajout du contrôleur de personne avec la génération de modèles automatique")

    *Ajout du contrôleur de personne avec la génération de modèles automatique*
9. Cliquez sur **ajouter** pour créer le nouveau contrôleur de personne avec la génération de modèles automatique. Vous avez généré les actions de contrôleur, ainsi que les vues.

    ![Après avoir créé le contrôleur de la personne avec la structure](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image5.png "après avoir créé le contrôleur de la personne avec la génération de modèles automatique")

    *Après avoir créé le contrôleur de la personne avec la génération de modèles automatique*
10. Ouvrez **PersonController** classe. Notez que les méthodes d’action CRUD complètes ont été générées automatiquement.

    ![À l’intérieur du contrôleur personne](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image6.png "contrôleur d’à l’intérieur de la personne")

    *À l’intérieur du contrôleur de la personne*

<a id="Ex1Task2"></a>

<a id="Task_2-_Running_the_application"></a>
#### <a name="task-2--running-the-application"></a>Tâche 2-exécuter l’application

À ce stade, la base de données n’est pas encore créé. Dans cette tâche, vous exécutez l’application pour la première fois et les opérations CRUD de test. La base de données est créé à la volée avec Code First.

1. Appuyez sur **F5** pour exécuter l’application.
2. Dans le navigateur, ajoutez **/Person** à l’URL pour ouvrir la page de la personne.

    ![Exécutez d’abord des application](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image7.png "tout d’abord l’exécution de l’Application")

    *L’application : exécutez d’abord*
3. Vous allez maintenant Explorer les pages de la personne et tester les opérations CRUD.

    1. Cliquez sur **créer un nouveau** pour ajouter une nouvelle personne. Entrez un prénom et nom de famille et cliquez sur **créer**.

        ![Ajout d’une nouvelle personne](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image8.png "Ajout d’une nouvelle personne")

        *Ajout d’une nouvelle personne*
    2. Dans la liste de la personne, vous pouvez supprimer, modifier ou ajouter des éléments.

        ![liste de personnes](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image9.png "liste de personnes")

        *Liste de personnes*
    3. Cliquez sur **détails** pour ouvrir les détails de la personne.

        ![Détails de la personne](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image10.png "les détails de la personne")

        *Détails de la personne*
4. Fermez le navigateur et revenez à Visual Studio. Notez que vous avez créé l’ensemble CRUD pour l’entité de la personne dans votre application - à partir du modèle pour les vues, sans avoir à écrire une seule ligne de code !

<a id="Ex1Task3"></a>

<a id="Task_3-_Updating_the_database_using_Entity_Framework_Migrations"></a>
#### <a name="task-3--updating-the-database-using-entity-framework-migrations"></a>3 de tâche-mise à jour la base de données à l’aide d’Entity Framework Migrations

Dans cette tâche, vous mettrez à jour la base de données à l’aide d’Entity Framework Migrations. Vous allez découvrir combien il est facile de modifier le modèle et de refléter les modifications apportées à vos bases de données à l’aide de la fonctionnalité d’Entity Framework Migrations.

1. Ouvrez la Console du Gestionnaire de Package. Sélectionnez **outils | Gestionnaire de Package de bibliothèque | Console du Gestionnaire de package**.
2. Dans la Console du Gestionnaire de Package, entrez la commande suivante :

    PMC

    [!code-powershell[Main](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/samples/sample2.ps1)]

    ![L’activation de Migrations](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image11.png "autorisant la migration")

    *L’activation de migrations*

    La commande Enable-Migration crée le **Migrations** dossier, qui contient un script pour initialiser la base de données.

    ![Dossier migrations](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image12.png "dossier Migrations")

    *Dossier migrations*
3. Ouvrez le **Configuration.cs** fichier dans le dossier Migrations. Recherchez le constructeur de classe et modifiez le **AutomaticMigrationsEnabled** valeur *true*.


    [!code-csharp[Main](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/samples/sample3.cs)]
4. Ouvrez la classe de personne et ajoutez un attribut pour le nom de personne intermédiaire. Avec ce nouvel attribut, vous modifiez le modèle.


    [!code-csharp[Main](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/samples/sample4.cs)]
5. Sélectionnez **générer | Générez la Solution** dans le menu pour générer l’application.

    ![Génération de l’application](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image13.png "génération de l’application")

    *Génération de l’application*
6. Dans la Console du Gestionnaire de Package, entrez la commande suivante :

    PMC

    [!code-powershell[Main](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/samples/sample5.ps1)]

    Cette commande recherche les modifications apportées à des objets de données, et ensuite, il ajoute les commandes nécessaires pour modifier la base de données en conséquence.

    ![Ajout d’un deuxième prénom](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image14.png "Ajout d’un deuxième prénom")

    *Ajout d’un deuxième prénom*
7. (Facultatif) Vous pouvez exécuter la commande suivante pour générer un script SQL avec la mise à jour différentiel. Cela vous permettra de mettre à jour manuellement de la base de données (dans ce cas il n’est pas nécessaire), ou appliquez les modifications dans les autres bases de données :

    PMC

    [!code-powershell[Main](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/samples/sample6.ps1)]

    ![Génération d’un script SQL](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image15.png "génération d’un script SQL")

    *Génération d’un script SQL*

    ![Mise à jour du Script SQL](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image16.png "mise à jour du Script SQL")

    *Mise à jour du Script SQL*
8. Dans la Console du Gestionnaire de Package, entrez la commande suivante pour mettre à jour la base de données :

    PMC

    [!code-powershell[Main](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/samples/sample7.ps1)]

    ![Mise à jour de la base de données](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image17.png "mise à jour de la base de données")

    *Mise à jour de la base de données*

    Cette opération ajoute le **MiddleName** colonne dans la **personnes** table pour correspondre à la définition actuelle de la **personne** classe.
9. Une fois la mise à jour de la base de données, cliquez sur le dossier du contrôleur, puis sélectionnez **ajouter | Contrôleur** pour ajouter le contrôleur de personne (complète avec les mêmes valeurs). Met à jour les méthodes existantes et les vues Ajout du nouvel attribut.

    ![Ajout d’une mise à jour du contrôleur](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image18.png "Ajout d’une mise à jour du contrôleur")

    *Mise à jour le contrôleur*
10. Cliquez sur **Ajouter**. Ensuite, sélectionnez les valeurs **PersonController.cs de remplacer** et **remplacer les vues associées** et cliquez sur **OK**.

    ![Ajout d’un remplacement de contrôleur](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image19.png)

    *Mise à jour le contrôleur*

<a id="Ex1Task4"></a>

<a id="Task4-_Running_the_application"></a>
#### <a name="task4--running-the-application"></a>Task4 - exécution de l’application

1. Appuyez sur **F5** pour exécuter l’application.
2. Ouvrez **/Person**. Notez que les données ont été préservées, tandis que la colonne de nom de jeune fille a été ajoutée.

    ![Nom de jeune fille ajouté](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image20.png "prénom ajouté")

    *Nom de jeune fille ajouté*
3. Si vous cliquez sur **modifier**, vous serez en mesure d’ajouter un deuxième prénom de la personne en cours.

    ![Nom de jeune fille édition](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image21.png "prénom edition")

* * *

<a id="Summary"></a>

<a id="Summary"></a>
## <a name="summary"></a>Résumé

Dans cet atelier, vous avez appris les étapes simples pour créer des opérations CRUD avec ASP.NET MVC 4 génération de modèles automatique à l’aide de n’importe quelle classe de modèle. Ensuite, vous avez appris comment effectuer une mise à jour de bout en bout dans votre application - à partir de la base de données pour les vues - à l’aide d’Entity Framework Migrations.

<a id="AppendixA"></a>

<a id="Appendix_A_Installing_Visual_Studio_Express_2012_for_Web"></a>
## <a name="appendix-a-installing-visual-studio-express-2012-for-web"></a>Annexe a : installation de Visual Studio Express 2012 pour le Web

Vous pouvez installer **Microsoft Visual Studio Express 2012 pour Web** ou un autre &quot;Express&quot; à l’aide de la version du  **[Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx)** . Les instructions suivantes vous guident à travers les étapes requises pour installer *Visual studio Express 2012 pour le Web* à l’aide de *Microsoft Web Platform Installer*.

1. Accédez à [ [https://go.microsoft.com/? linkid = 9810169](https://go.microsoft.com/?linkid=9810169)](https://go.microsoft.com/?linkid=9810169). Sinon, si vous avez déjà installé Web Platform Installer, vous pouvez ouvrir il et recherchez le produit &quot; *Visual Studio Express 2012 pour le Web avec Windows Azure SDK*&quot;.
2. Cliquez sur **installer maintenant**. Si vous n’avez pas **Web Platform Installer** vous allez être redirigé pour télécharger et installer tout d’abord.
3. Une fois **Web Platform Installer** est ouvert, cliquez sur **installer** pour démarrer le programme d’installation.

    ![Installer Visual Studio Express](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image22.png "installer Visual Studio Express")

    *Installer Visual Studio Express*
4. Lisez les termes et les licences de tous les produits et cliquez sur **J’accepte** pour continuer.

    ![Accepter les termes du contrat de licence](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image23.png)

    *Accepter les termes du contrat de licence*
5. Attendez que le processus de téléchargement et l’installation se termine.

    ![Progression de l'installation](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image24.png)

    *Progression de l’installation*
6. Une fois l’installation terminée, cliquez sur **Terminer**.

    ![Installation est terminée](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image25.png)

    *Installation est terminée*
7. Cliquez sur **Exit** fermer Web Platform Installer.
8. Pour ouvrir Visual Studio Express pour le Web, accédez à la **Démarrer** écran et démarrer l’écriture &quot; **VS Express**&quot;, puis cliquez sur le **Visual Studio Express pour le Web** vignette.

    ![VS Express pour la vignette du Web](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image26.png)

    *VS Express pour la vignette du Web*

<a id="AppendixB"></a>

<a id="Appendix_B_Using_Code_Snippets"></a>
## <a name="appendix-b-using-code-snippets"></a>Annexe b : à l’aide d’extraits de Code

Avec des extraits de code, vous avez tout le code que vous avez besoin. Le document lab vous indique exactement quand vous pouvez les utiliser, comme indiqué dans l’illustration suivante.

![À l’aide d’extraits de code Visual Studio pour insérer du code dans votre projet](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image27.png "des extraits de code à l’aide de Visual Studio pour insérer du code dans votre projet")

*À l’aide d’extraits de code Visual Studio pour insérer du code dans votre projet*

***Pour ajouter un extrait de code à l’aide du clavier (c# uniquement)***

1. Placez le curseur où vous souhaitez insérer le code.
2. Commencez à taper le nom de l’extrait de code (sans espaces ou des traits d’union).
3. Observez comment IntelliSense affiche les noms des extraits de code de mise en correspondance.
4. Sélectionnez l’extrait de code correct (ou continuez à taper jusqu'à ce que le nom de l’extrait de code entier est sélectionné).
5. Appuyez sur la touche Tab à deux reprises pour insérer l’extrait de code à l’emplacement du curseur.

![Commencez à taper le nom de l’extrait de code](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image28.png "commencez à taper le nom de l’extrait de code")

*Commencez à taper le nom de l’extrait de code*

![Appuyez sur Tab pour sélectionner l’extrait de code en surbrillance](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image29.png "appuyez sur Tab pour sélectionner l’extrait de code en surbrillance")

*Appuyez sur Tab pour sélectionner l’extrait de code en surbrillance*

![Appuyez sur Tab à nouveau et l’extrait de code sont développés](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image30.png "appuyez sur Tab à nouveau et l’extrait de code seront développe.")

*Appuyez sur Tab à nouveau et l’extrait de code seront développe.*

***Pour ajouter un extrait de code à l’aide de la souris (c#, Visual Basic et XML)*** 1. Clic droit où vous souhaitez insérer l’extrait de code.

1. Sélectionnez **insérer un extrait** suivie **mes extraits de Code**.
2. Sélectionnez l’extrait de code approprié dans la liste, en cliquant dessus.

![Avec le bouton sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image31.png "avec le bouton sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait")

*Avec le bouton droit sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait*

![Sélectionnez l’extrait de code approprié dans la liste, en cliquant dessus](aspnet-mvc-4-entity-framework-scaffolding-and-migrations/_static/image32.png "choisir l’extrait de code approprié dans la liste, en cliquant sur celle-ci")

*Sélectionnez l’extrait de code approprié dans la liste, en cliquant sur celle-ci*
