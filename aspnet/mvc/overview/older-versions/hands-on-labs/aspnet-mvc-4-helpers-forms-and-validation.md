---
uid: mvc/overview/older-versions/hands-on-labs/aspnet-mvc-4-helpers-forms-and-validation
title: "Programmes d’assistance ASP.NET MVC 4, formulaires et la Validation | Documents Microsoft"
author: rick-anderson
description: "Dans ASP.NET MVC 4 modèles et les ateliers pratiques de données Access, vous avez été le chargement et affichage des données à partir de la base de données. Dans cet atelier pratique, vous allez ajouter à la..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/18/2013
ms.topic: article
ms.assetid: 187ee9cd-bc70-479b-bfed-f568b8da96eb
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/hands-on-labs/aspnet-mvc-4-helpers-forms-and-validation
msc.type: authoredcontent
ms.openlocfilehash: 4dd10430778dc51fef1199315ee02eb2cd4970ba
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-mvc-4-helpers-forms-and-validation"></a>Validation, les formulaires et les programmes d’assistance ASP.NET MVC 4
====================
par [Web Camps équipe](https://twitter.com/webcamps)

> Dans **accès aux données et les modèles ASP.NET MVC 4** atelier pratique, vous avez été le chargement et l’affichage des données à partir de la base de données. Dans cet atelier pratique, vous allez ajouter à la **magasin de musique** application la possibilité de modifier ces données.
> 
> Objectifs à l’esprit, vous allez d’abord créer le contrôleur qui prend en charge les actions de création, lecture, mise à jour et supprimer (CRUD) des albums. Vous allez générer un modèle de vue d’Index en tirant parti de la fonctionnalité de génération de modèles automatique de ASP.NET MVC pour afficher les propriétés des albums dans une table HTML. Pour des raisons de cette vue, vous allez ajouter un programme d’assistance HTML personnalisé qui va tronquer les longues descriptions.
> 
> Vous ajouterez par la suite, le modifier et créer des vues qui permettent de modifier les albums dans la base de données, à l’aide des éléments de formulaire tels que des listes déroulantes.
> 
> Enfin, vous permet aux utilisateurs de supprimer un album et vous serez également les empêcher d’entrer des données erronées en validant leur entrée.
> 
> > [!NOTE]
> > Cet atelier pratique suppose que vous avez une connaissance élémentaire des **ASP.NET MVC**. Si vous n’avez pas utilisé **ASP.NET MVC** auparavant, nous vous recommandons de dépasser **notions de base ASP.NET MVC** atelier pratique.
> 
> 
> Ce laboratoire présente les améliorations et nouvelles fonctionnalités décrites précédemment en appliquant les modifications mineures à un exemple d’application Web dans le dossier Source.
> 
> Tous les exemples de code et des extraits de code sont inclus dans le Kit de formation Camps Web, disponible à l’adresse [https://www.microsoft.com/en-us/download/29843](https://www.microsoft.com/en-us/download/29843).


<a id="Objectives"></a>

<a id="Objectives"></a>
### <a name="objectives"></a>Objectifs

Dans cet atelier pratique, vous allez apprendre comment :

- Créer un contrôleur pour prendre en charge les opérations CRUD
- Générer une vue d’Index pour afficher les propriétés de l’entité dans une table HTML
- Ajouter un programme d’assistance HTML personnalisé
- Créer et personnaliser une vue à modifier
- Différence entre les méthodes d’action qui réagissent appels HTTP-POST ou HTTP-GET
- Ajouter et personnaliser une instruction Create View
- Handle de la suppression d’une entité
- Valider l’entrée d’utilisateur

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

Les exercices suivants composent cet atelier pratique :

1. [Création du contrôleur du Gestionnaire de magasin et sa vue de l’Index](#Exercise1)
2. [Ajout d’une application d’assistance HTML](#Exercise2)
3. [Création de la vue de modification](#Exercise3)
4. [Ajout d’une vue de créer](#Exercise4)
5. [Suppression de la gestion](#Exercise5)
6. [Ajout de la validation](#Exercise6)
7. [À l’aide de jQuery non obstructive côté Client](#Exercise7)

> [!NOTE]
> Chaque exercice est accompagné par un **fin** dossier qui contient la solution résultante, vous devez obtenir après avoir effectué les exercices. Si vous avez besoin d’aide fonctionne via les exercices, vous pouvez utiliser cette solution comme guide.


Durée estimée pour effectuer ce laboratoire : **60 minutes**

<a id="Exercise1"></a>

<a id="Exercise_1_Creating_the_Store_Manager_controller_and_its_Index_view"></a>
### <a name="exercise-1-creating-the-store-manager-controller-and-its-index-view"></a>Exercice 1 : Création du contrôleur du Gestionnaire de magasin et sa vue de l’Index

Dans cet exercice, vous allez apprendre à créer un nouveau contrôleur pour prendre en charge les opérations CRUD, personnaliser sa méthode d’action Index pour retourner une liste d’albums à partir de la base de données et la génération d’un modèle de vue d’Index en tirant parti de la génération de modèles automatique de ASP.NET MVC enfin fonction pour afficher les propriétés des albums dans une table HTML.

<a id="Ex1Task1"></a>

<a id="Task_1_-_Creating_the_StoreManagerController"></a>
#### <a name="task-1---creating-the-storemanagercontroller"></a>Tâche 1 : création de la StoreManagerController

Dans cette tâche, vous allez créer un nouveau contrôleur appelé **StoreManagerController** pour prendre en charge les opérations CRUD.

1. Ouvrez le **commencer** solution situé dans **début/CreatingTheStoreManagerController-Ex1/Source/** dossier.

    1. Vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Ajoutez un nouveau contrôleur. Pour ce faire, cliquez sur le **contrôleurs** dossier dans l’Explorateur de solutions, sélectionnez **ajouter** , puis le **contrôleur** commande. Modifier la **contrôleur** **nom** à **StoreManagerController** et assurez-vous que l’option **contrôleur MVC avec des actions de lecture/écriture vides**est sélectionnée. Cliquez sur **Ajouter**.

    ![Boîte de dialogue Ajouter contrôleur](aspnet-mvc-4-helpers-forms-and-validation/_static/image1.png "boîte de dialogue Ajouter contrôleur")

    *Boîte de dialogue contrôleur ajouter*

    Une nouvelle classe de contrôleur est générée. Étant donné que vous avez indiquée pour ajouter des actions en lecture/écriture, les méthodes stub, des actions CRUD courantes sont créées avec des commentaires TODO renseignés, vous invitant à inclure la logique d’application spécifique.

<a id="Ex1Task2"></a>

<a id="Task_2_-_Customizing_the_StoreManager_Index"></a>
#### <a name="task-2---customizing-the-storemanager-index"></a>Tâche 2 : personnalisation de l’Index StoreManager

Dans cette tâche, vous allez personnaliser la méthode d’action StoreManager Index pour retourner une vue avec la liste des albums à partir de la base de données.

1. Dans la classe StoreManagerController, ajoutez le code suivant *à l’aide de* directives.

    (Code d’extrait de code - *Validation - Ex1, les formulaires et les programmes d’assistance de ASP.NET MVC 4 à l’aide de MvcMusicStore*)

    [!code-csharp[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample1.cs)]
2. Ajoutez un champ à la **StoreManagerController** pour stocker une instance de **MusicStoreEntities.**

    (Code d’extrait de code - *d’applications auxiliaires ASP.NET MVC 4 et de formulaires et de Validation - Ex1 MusicStoreEntities*)

    [!code-csharp[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample2.cs)]
3. Implémentation de l’action de StoreManagerController Index pour retourner une vue avec la liste des albums.

    La logique d’action de contrôleur sera très similaire à l’action d’Index de la StoreController écrite précédemment. Utilisez LINQ pour récupérer tous les albums, y compris les informations de Genre et artiste pour l’affichage.

    (Code d’extrait de code - *Validation - Ex1 StoreManagerController Index, les formulaires et les programmes d’assistance ASP.NET MVC 4*)

    [!code-csharp[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample3.cs)]

<a id="Ex1Task3"></a>

<a id="strongTask_3_-_Creating_the_Index_Viewstrong"></a>
#### <a name="task-3---creating-the-index-view"></a>Tâche 3 : création de la vue d’Index

Dans cette tâche, vous allez créer le modèle d’affichage de l’Index pour afficher la liste des albums retourné par le **StoreManager** contrôleur.

1. Avant de créer le nouveau modèle d’affichage, vous devez générer le projet afin que les **boîte de dialogue Vue ajouter** connaît le **Album** classe à utiliser. Sélectionnez **générer | Build MvcMusicStore** pour générer le projet.
2. Avec le bouton droit à l’intérieur de la **Index** méthode d’action et sélectionnez **ajouter une vue**. Cela affiche la **ajouter une vue** boîte de dialogue.

    ![Ajouter une vue](aspnet-mvc-4-helpers-forms-and-validation/_static/image2.png "ajouter une vue")

    *Ajout d’une vue à partir de l’Index (méthode)*
3. Dans la boîte de dialogue Ajouter une vue, vérifiez que le nom de la vue est **Index**. Sélectionnez le **créer une vue fortement typée** option, puis sélectionnez **Album (MvcMusicStore.Models)** à partir de la **classe de modèle** liste déroulante. Sélectionnez **liste** à partir de la **modèle de vue de structure** liste déroulante. Laissez le **moteur d’affichage** à **Razor** et les autres champs leur valeur par défaut de valeur, puis **ajouter**.

    ![Ajout d’une vue d’index](aspnet-mvc-4-helpers-forms-and-validation/_static/image3.png "Ajout d’une vue d’index")

    *Ajout d’une vue d’Index*

<a id="Ex1Task4"></a>

<a id="Task_4_-_Customizing_the_scaffold_of_the_Index_View"></a>
#### <a name="task-4---customizing-the-scaffold-of-the-index-view"></a>Tâche 4 : personnalisation de la vue de la structure de la vue d’Index

Dans cette tâche, vous pouvez ajuster le modèle d’affichage simple créé avec la fonctionnalité de génération de modèles automatique ASP.NET MVC pour afficher les champs que vous souhaitez.

> [!NOTE]
> Le **la structure** prise en charge dans ASP.NET MVC génère un modèle d’affichage simple qui répertorie tous les champs dans le modèle de l’Album. **Génération de modèles automatique** fournit un moyen rapide de commencer à utiliser une vue fortement typée : au lieu de devoir écrire le modèle d’affichage manuellement, la génération de modèles automatique rapidement génère un modèle par défaut et vous pouvez ensuite modifier le code généré.


1. Passez en revue le code créé. La liste de champs générées feront partie des éléments suivants de table HTML qui **la structure** utilise pour afficher les données tabulaires.

    [!code-cshtml[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample4.cshtml)]
2. Remplacez le  **&lt;table&gt;**  code avec le code suivant pour afficher uniquement les **Genre**, **artiste**, **titre d’Album**, et **prix** champs. Cette opération supprime le **AlbumId** et **Album Art URL** colonnes. En outre, il modifie les colonnes GenreId et ArtistId pour afficher leurs propriétés de classe lié de **Artist.Name** et **Genre.Name**et supprime la **détails** lien.

    [!code-cshtml[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample5.cshtml)]
3. Modifier les descriptions suivantes.

    [!code-cshtml[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample6.cshtml)]

<a id="Ex1Task5"></a>

<a id="Task_5_-_Running_the_Application"></a>
#### <a name="task-5---running-the-application"></a>Tâche 5 : exécution de l’Application

Dans cette tâche, vous allez tester que le **StoreManager** **Index** modèle d’affichage affiche la liste des albums en fonction de la conception des étapes précédentes.

1. Appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Modifier l’URL pour **/StoreManager** pour vérifier qu’une liste d’albums s’affiche, indiquant leur **titre**, **artiste** et **Genre**.

    ![Parcourir la liste des albums](aspnet-mvc-4-helpers-forms-and-validation/_static/image4.png "parcourir la liste des albums")

    *Parcourir la liste des albums*

<a id="Exercise2"></a>

<a id="Exercise_2_Adding_an_HTML_Helper"></a>
### <a name="exercise-2-adding-an-html-helper"></a>Exercice 2 : Ajout d’une application d’assistance HTML

La page d’Index de StoreManager a un problème potentiel : propriétés du titre et le nom de l’auteur peuvent tous deux être suffisamment longue pour décaler la mise en forme de table. Dans cet exercice, vous allez apprendre à ajouter un programme d’assistance HTML personnalisé pour tronquer ce texte.

Dans l’illustration suivante, vous pouvez voir la manière dont le format est modifié en raison de la longueur du texte lorsque vous utilisez une taille de navigateur petit.

![Parcourir la liste des Albums avec pas tronqué texte](aspnet-mvc-4-helpers-forms-and-validation/_static/image5.png "parcourir la liste des Albums avec ne tronqué pas de texte")

*Parcourir la liste des Albums avec ne tronqué pas de texte*

<a id="Ex2Task1"></a>

<a id="Task_1_-_Extending_the_HTML_Helper"></a>
#### <a name="task-1---extending-the-html-helper"></a>Tâche 1 - extension de l’application d’assistance HTML

Dans cette tâche, vous allez ajouter une nouvelle méthode **Truncate** à la **HTML** objet exposé dans les vues MVC ASP.NET. Pour ce faire, vous allez implémenter une **méthode d’extension** à la fonction intégrée **System.Web.Mvc.HtmlHelper** classe fournie par ASP.NET MVC.

> [!NOTE]
> Pour en savoir plus sur **les méthodes d’Extension**, consultez cet article msdn. [https://msdn.Microsoft.com/en-us/library/bb383977.aspx](https://msdn.microsoft.com/en-us/library/bb383977.aspx).


1. Ouvrez le **commencer** solution situé dans **début/AddingAnHTMLHelper-Ex2/Source/** dossier. Dans le cas contraire, vous pouvez continuer à utiliser le **fin** solution obtenue par la fin de l’exercice précédent.

    1. Si vous avez ouvert le **commencer** solution, vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Index de StoreManager afficher. Pour ce faire, dans l’Explorateur de solutions, développez le **vues** dossier, puis le **StoreManager** et ouvrez le **Index.cshtml** fichier.
3. Ajoutez le code suivant ci-dessous le  **@model**  la directive pour définir le **Truncate** méthode d’assistance.

    [!code-cshtml[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample7.cshtml)]

<a id="Ex2Task2"></a>

<a id="Task_2_-_Truncating_Text_in_the_Page"></a>
#### <a name="task-2---truncating-text-in-the-page"></a>Tâche 2 : troncation de texte dans la Page

Dans cette tâche, vous allez utiliser le **Truncate** méthode tronquer le texte dans le modèle d’affichage.

1. Index de StoreManager afficher. Pour ce faire, dans l’Explorateur de solutions, développez le **vues** dossier, puis le **StoreManager** et ouvrez le **Index.cshtml** fichier.
2. Remplacez les lignes qui indiquent la **nom artiste** et Album **titre**. Pour ce faire, remplacez les lignes suivantes.

    [!code-cshtml[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample8.cshtml)]

<a id="Ex2Task3"></a>

<a id="Task_3_-_Running_the_Application"></a>
#### <a name="task-3---running-the-application"></a>Tâche 3 : exécution de l’Application

Dans cette tâche, vous allez tester que le **StoreManager** **Index** afficher le modèle tronque le titre et nom de l’auteur de l’Album.

1. Appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Modifier l’URL pour **/StoreManager** pour vérifier ce long textes en le **titre** et **artiste** colonne sont tronqués.

    ![Tronqué noms titres et les artistes](aspnet-mvc-4-helpers-forms-and-validation/_static/image6.png "tronqué noms artistes et de titres")

    *Les noms d’artiste et les titres tronquées*

<a id="Exercise3"></a>

<a id="Exercise_3_Creating_the_Edit_View"></a>
### <a name="exercise-3-creating-the-edit-view"></a>Exercice 3 : Création de la vue de modification

Dans cet exercice, vous allez apprendre à créer un formulaire pour permettre aux responsables de magasin modifier un Album. Ils seront parcourir le **/StoreManager/Edit/id** URL (**id** en cours de l’id unique de l’album pour modifier), créant ainsi un appel HTTP-GET sur le serveur.

La méthode d’action contrôleur modifier se récupérer l’Album approprié à partir de la base de données, créez un **StoreManagerViewModel** objet à encapsuler (ainsi que d’une liste d’artistes et Genres), puis transmettre à un modèle d’affichage afficher la page HTML à l’utilisateur. Cette page contient un  **&lt;formulaire&gt;**  élément avec les zones de texte et les listes déroulantes pour modifier les propriétés de l’Album.

Une fois que l’utilisateur met à jour les valeurs de formulaire Album et clique sur le **enregistrer** bouton, les modifications sont envoyées une requête HTTP-POST rappeler **/StoreManager/Edit/id**. Bien que l’URL reste la même que dans le dernier appel, ASP.NET MVC qui identifie ce temps est une requête HTTP-POST et par conséquent exécute une autre méthode d’action de modification (un décorée avec **[HttpPost]**).

<a id="Ex3Task1"></a>

<a id="Task_1_-_Implementing_the_HTTP-GET_Edit_Action_Method"></a>
#### <a name="task-1---implementing-the-http-get-edit-action-method"></a>Tâche 1 : implémentation de la méthode d’Action HTTP-GET Edition

Dans cette tâche, vous allez implémenter la version HTTP-GET de la méthode d’action de modification pour récupérer l’Album approprié à partir de la base de données, ainsi qu’une liste de tous les Genres et artistes. Il sera empaqueter ces données dans le **StoreManagerViewModel** objet défini dans la dernière étape, qui est ensuite transmise à un modèle d’affichage pour afficher la réponse avec.

1. Ouvrez le **commencer** solution situé dans **début/CreatingTheEditView-Ex3/Source/** dossier. Dans le cas contraire, vous pouvez continuer à utiliser le **fin** solution obtenue par la fin de l’exercice précédent.

    1. Si vous avez ouvert le **commencer** solution, vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Ouvrez le **StoreManagerController** classe. Pour ce faire, développez le **contrôleurs** et double-cliquez sur **StoreManagerController.cs**.
3. Remplacez le **HTTP-GET modifier** méthode d’action avec le code suivant pour récupérer les **Album** , ainsi que les **Genres** et **artistes**répertorie.

    (Code d’extrait de code - *Validation - Ex3 StoreManagerController HTTP-GET modifier l’action et des formulaires et des programmes d’assistance de ASP.NET MVC 4*)

    [!code-csharp[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample9.cs)]

    > [!NOTE]
    > Vous utilisez **System.Web.Mvc** **SelectList** pour artistes et Genres au lieu du **System.Collections.Generic** liste.
    > 
    > **SelectList** est un moyen plus NET pour remplir des listes déroulantes HTML et de gérer des éléments comme la sélection actuelle. L’instanciation et la configuration ultérieure de ces objets ViewModel dans l’action du contrôleur rend le scénario d’écran de modifier plus claire.

<a id="Ex3Task2"></a>

<a id="Task_2_-_Creating_the_Edit_View"></a>
#### <a name="task-2---creating-the-edit-view"></a>Tâche 2 : création de la vue de modification

Dans cette tâche, vous allez créer un modèle de modifier la vue qui affiche les propriétés de l’album ultérieurement.

1. Créer le mode édition. Pour ce faire, cliquez à l’intérieur de la **modifier** méthode d’action et sélectionnez **ajouter une vue**.
2. Dans la boîte de dialogue Ajouter une vue, vérifiez que le nom de la vue est **modifier**. Vérifiez le **créer une vue fortement typée** case à cocher et sélectionnez **Album (MvcMusicStore.Models)** à partir de la **afficher la classe de données** liste déroulante. Sélectionnez **modifier** à partir de la **modèle de vue de structure** liste déroulante. Les autres champs avec leur valeur par défaut et cliquez sur **ajouter**.

    ![Ajout d’une vue d’édition](aspnet-mvc-4-helpers-forms-and-validation/_static/image7.png "Ajout d’une vue d’édition")

    *Ajout d’une vue d’édition*

<a id="Ex3Task3"></a>

<a id="Task_3_-_Running_the_Application"></a>
#### <a name="task-3---running-the-application"></a>Tâche 3 : exécution de l’Application

Dans cette tâche, vous allez tester que le **StoreManager** **modifier** afficher la page affiche les valeurs des propriétés de l’album passé comme paramètre.

1. Appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Modifier l’URL pour **/StoreManager/Edit/1** pour vérifier que les valeurs des propriétés de l’album passé sont affichées.

    ![Parcourir vue d’édition Album](aspnet-mvc-4-helpers-forms-and-validation/_static/image8.png "modifier la vue Album de navigation")

    *Vue d’édition Album de navigation*

<a id="Ex3Task4"></a>

<a id="Task_4_-_Implementing_drop-downs_on_the_Album_Editor_Template"></a>
#### <a name="task-4---implementing-drop-downs-on-the-album-editor-template"></a>Tâche 4 : mise en œuvre les zones déroulantes sur le modèle de l’éditeur de l’Album

Dans cette tâche, vous allez ajouter des listes déroulantes pour le modèle d’affichage créé dans la dernière tâche, afin que l’utilisateur peut sélectionner dans une liste d’artistes et Genres.

1. Remplacer tout le **Album** fieldset par le code suivant :

    [!code-cshtml[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample10.cshtml)]

    > [!NOTE]
    > Un **Html.DropDownList** helper a été ajouté pour afficher les zones déroulantes permettant de choisir les artistes et Genres. Les paramètres transmis à **Html.DropDownList** sont :
    > 
    > 1. Le nom du champ de formulaire (**&quot;ArtistId&quot;**).
    > 2. Le **SelectList** de valeurs pour la liste déroulante.

<a id="Ex3Task5"></a>

<a id="Task_5_-_Running_the_Application"></a>
#### <a name="task-5---running-the-application"></a>Tâche 5 : exécution de l’Application

Dans cette tâche, vous allez tester que le **StoreManager** **modifier** afficher la page affiche les zones déroulantes au lieu de champs de texte artiste et l’ID de Genre.

1. Appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Modifier l’URL pour **/StoreManager/Edit/1** pour vérifier qu’elle affiche les zones déroulantes au lieu de champs de texte artiste et l’ID de Genre.

    ![Modifier la vue Album avec les zones de liste déroulante de navigation](aspnet-mvc-4-helpers-forms-and-validation/_static/image9.png "modifier la vue Album de navigation avec les zones de liste déroulante")

    *Modifier cet affichage navigation Album, avec des listes déroulantes*

<a id="Ex3Task6"></a>

<a id="Task_6_-_Implementing_the_HTTP-POST_Edit_action_method"></a>
#### <a name="task-6---implementing-the-http-post-edit-action-method"></a>Tâche 6 : implémentation de la méthode d’action HTTP-POST modifier

Maintenant que la vue à modifier s’affiche comme prévu, vous devez implémenter la méthode de modifier l’Action HTTP-POST pour enregistrer les modifications apportées à l’Album.

1. Fermez le navigateur si nécessaire, pour revenir à la fenêtre Visual Studio. Ouvrez **StoreManagerController** à partir de la **contrôleurs** dossier.
2. Remplacez **HTTP-POST modifier** action méthode par le code suivant (Notez que la méthode qui doit être remplacée est une version surchargée qui reçoit deux paramètres) :

    (Code d’extrait de code - *Validation - Ex3 StoreManagerController HTTP-POST modifier l’action et des formulaires et des programmes d’assistance de ASP.NET MVC 4*)

    [!code-csharp[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample11.cs)]

    > [!NOTE]
    > Cette méthode est exécutée quand l’utilisateur clique sur le **enregistrer** bouton de la vue et effectue une requête HTTP-POST des valeurs de formulaire sur le serveur pour les conserver dans la base de données. Le decorator **[HttpPost]** indique que la méthode doit être utilisée pour les scénarios HTTP-POST. La méthode prend un **Album** objet. ASP.NET MVC crée automatiquement l’objet Album à partir de la validé &lt;formulaire&gt; valeurs.
    > 
    > La méthode va effectuer ces étapes :
    > 
    > 1. Si le modèle est valide :
    > 
    >     1. Mettre à jour l’entrée de l’album dans le contexte pour la marquer comme un objet modifié.
    >     2. Enregistrer les modifications et rediriger vers la vue index.
    > 2. Si le modèle n’est pas valide, il remplit l’élément ViewBag avec la **GenreId** et **ArtistId**, elle retourne la vue avec l’objet Album reçu pour autoriser l’utilisateur effectuer toute mise à jour requise.

<a id="Ex3Task7"></a>

<a id="Task_7_-_Running_the_Application"></a>
#### <a name="task-7---running-the-application"></a>Tâche 7 : exécution de l’Application

Dans cette tâche, vous allez tester que le **StoreManager modifier** page de vue enregistre réellement les données Album mis à jour dans la base de données.

1. Appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Modifier l’URL pour **/StoreManager/Edit/1**. Remplacez le titre d’Album par **charge** , puis cliquez sur **enregistrer**. Vérifiez que le titre d’album réellement modifié dans la liste des albums.

    ![Mise à jour un album](aspnet-mvc-4-helpers-forms-and-validation/_static/image10.png "mise à jour d’un album")

    *Mise à jour d’un Album*

<a id="Exercise4"></a>

<a id="Exercise_4_Adding_a_Create_View"></a>
### <a name="exercise-4-adding-a-create-view"></a>Exercice 4 : Ajout d’une vue de créer

Maintenant que le **StoreManagerController** prend en charge la **modifier** capacité, dans cet exercice, vous allez apprendre à ajouter un modèle Create View pour vous permettre de stocker les gestionnaires ajouter les nouveaux Albums à l’application.

Comme vous le faisiez avec les fonctionnalités d’édition, vous allez implémenter le scénario de créer à l’aide de deux méthodes distinctes dans le **StoreManagerController** classe :

1. Une méthode d’action affiche un formulaire vide lorsque le magasin de gestionnaires d’abord visiter le **/StoreManager/créer** URL.
2. Une deuxième méthode d’action gère le scénario où le directeur du magasin clique sur le **enregistrer** bouton dans le formulaire et soumet à nouveau les valeurs du **/StoreManager/créer** URL sous la forme d’une requête HTTP-POST.

<a id="Ex4Task1"></a>

<a id="Task_1_-_Implementing_the_HTTP-GET_Create_action_method"></a>
#### <a name="task-1---implementing-the-http-get-create-action-method"></a>Tâche 1 : implémentation de la méthode d’action HTTP-GET créer

Dans cette tâche, vous allez implémenter la version HTTP-GET de la méthode d’action Create pour récupérer une liste de tous les Genres et artistes, empaqueter ces données dans un **StoreManagerViewModel** objet, qui est ensuite transmis à un modèle d’affichage.

1. Ouvrez le **commencer** solution situé dans **Source/Ex4-AddingACreateView/début/** dossier. Dans le cas contraire, vous pouvez continuer à utiliser le **fin** solution obtenue par la fin de l’exercice précédent.

    1. Si vous avez ouvert le **commencer** solution, vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Ouvrez **StoreManagerController** classe. Pour ce faire, développez le **contrôleurs** et double-cliquez sur **StoreManagerController.cs**.
3. Remplacez le **créer** code de méthode d’action avec les éléments suivants :

    (Code d’extrait de code - *Validation - action de création de Ex4 StoreManagerController HTTP-GET, les formulaires et les programmes d’assistance de ASP.NET MVC 4*)

    [!code-csharp[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample12.cs)]

<a id="Ex4Task2"></a>

<a id="Task_2_-_Adding_the_Create_View"></a>
#### <a name="task-2---adding-the-create-view"></a>Tâche 2 : ajout de la vue de créer

Dans cette tâche, vous allez ajouter le modèle de créer une vue qui affiche un nouveau formulaire Album (vide).

1. Avec le bouton droit à l’intérieur de la **créer** méthode d’action et sélectionnez **ajouter une vue**. Cela affiche la boîte de dialogue Ajouter une vue.
2. Dans la boîte de dialogue Ajouter une vue, vérifiez que le nom de la vue est **créer**. Sélectionnez le **créer une vue fortement typée** option et sélectionnez **Album (MvcMusicStore.Models)** à partir de la **classe de modèle** liste déroulante et **créer** à partir de la **modèle de vue de structure** liste déroulante. Les autres champs avec leur valeur par défaut et cliquez sur **ajouter**.

    ![Ajout d’une vue de créer](aspnet-mvc-4-helpers-forms-and-validation/_static/image11.png "ajout-a-créer-view.png")

    *Ajout de la vue de créer*
3. Mise à jour la **GenreId** et **ArtistId** champs à utiliser une liste déroulante, comme indiqué ci-dessous :

    [!code-cshtml[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample13.cshtml)]

<a id="Ex4Task3"></a>

<a id="Task_3_-_Running_the_Application"></a>
#### <a name="task-3---running-the-application"></a>Tâche 3 : exécution de l’Application

Dans cette tâche, vous allez tester que le **StoreManager** **créer** afficher la page affiche un formulaire Album vide.

1. Appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Modifier l’URL pour **/StoreManager/créer**. Vérifiez qu’un formulaire vide s’affiche pour remplir les nouvelles propriétés de l’Album.

    ![Créer un affichage avec un formulaire vide](aspnet-mvc-4-helpers-forms-and-validation/_static/image12.png "créer une vue avec un formulaire vide")

    *Créer un affichage avec un formulaire vide*

<a id="Ex4Task4"></a>

<a id="Task_4_-_Implementing_the_HTTP-POST_Create_Action_Method"></a>
#### <a name="task-4---implementing-the-http-post-create-action-method"></a>Tâche 4 : implémentation de la requête HTTP-POST créer la méthode d’Action

Dans cette tâche, vous allez implémenter la version HTTP-POST de la méthode d’action de création qui sera appelée quand un utilisateur clique sur le **enregistrer** bouton. La méthode doit enregistrer le nouvel album dans la base de données.

1. Fermez le navigateur si nécessaire, pour revenir à la fenêtre Visual Studio. Ouvrez **StoreManagerController** classe. Pour ce faire, développez le **contrôleurs** et double-cliquez sur **StoreManagerController.cs**.
2. Remplacez **HTTP-POST créer** code de méthode d’action avec les éléments suivants :

    (Code d’extrait de code - *Validation - Ex4 StoreManagerController HTTP - POST permet de créer une action, les formulaires et les programmes d’assistance de ASP.NET MVC 4*)

    [!code-csharp[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample14.cs)]

    > [!NOTE]
    > L’action de création est assez similaire à la méthode d’action de modification précédente, mais au lieu de définir l’objet modifié, il est ajouté au contexte.

<a id="Ex4Task5"></a>

<a id="Task_5_-_Running_the_Application"></a>
#### <a name="task-5---running-the-application"></a>Tâche 5 : exécution de l’Application

Dans cette tâche, vous allez tester que le **StoreManager créer** afficher la page vous permet de créer un nouvel Album et redirige ensuite à la vue d’Index StoreManager.

1. Appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Modifier l’URL pour **/StoreManager/créer**. Remplir les champs de formulaire de données pour un nouvel Album, à celui illustré dans la figure suivante :

    ![Création d’un Album](aspnet-mvc-4-helpers-forms-and-validation/_static/image13.png "création d’un Album")

    *Création d’un Album*
3. Vérifiez que vous êtes redirigé vers la vue Index StoreManager qui inclut le nouvel Album venez de créer.

    ![Nouvel Album créé](aspnet-mvc-4-helpers-forms-and-validation/_static/image14.png "Nouvel Album créé")

    *Nouvel Album créé*

<a id="Exercise5"></a>

<a id="Exercise_5_Handling_Deletion"></a>
### <a name="exercise-5-handling-deletion"></a>Exercice 5 : Gestion de la suppression

La possibilité de supprimer des albums n’est pas encore implémentée. Voici ce que cet exercice sera sur. Comme avant, vous allez implémenter le scénario de suppression à l’aide de deux méthodes distinctes dans le **StoreManagerController** classe :

1. Une méthode d’action affiche un écran de confirmation
2. Une deuxième méthode d’action gère l’envoi du formulaire

<a id="Ex5Task1"></a>

<a id="Task_1_-_Implementing_the_HTTP-GET_Delete_Action_Method"></a>
#### <a name="task-1---implementing-the-http-get-delete-action-method"></a>Tâche 1 : implémentation de la méthode d’Action Delete HTTP-GET

Dans cette tâche, vous allez implémenter la version HTTP-GET de la méthode d’action de suppression pour récupérer les informations de l’album.

1. Ouvrez le **commencer** solution situé dans **début/HandlingDeletion-Ex5/Source/** dossier. Dans le cas contraire, vous pouvez continuer à utiliser le **fin** solution obtenue par la fin de l’exercice précédent.

    1. Si vous avez ouvert le **commencer** solution, vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Ouvrez **StoreManagerController** classe. Pour ce faire, développez le **contrôleurs** et double-cliquez sur **StoreManagerController.cs**.
3. La contrôleur action Delete est exactement le même que l’action de contrôleur précédente détails du magasin : elle interroge le **album** objet à partir de la base de données à l’aide de la **id** fourni dans l’URL et retourne le approprié **vue**. Pour ce faire, remplacez la méthode HTTP-GET **supprimer** code de méthode d’action avec les éléments suivants :

    (Code d’extrait de code - *Validation - Ex5 gestion suppression HTTP-GET supprimer une action, les formulaires et les programmes d’assistance de ASP.NET MVC 4*)

    [!code-csharp[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample15.cs)]
4. Avec le bouton droit à l’intérieur de la **supprimer** méthode d’action et sélectionnez **ajouter une vue**. Cela affiche la boîte de dialogue Ajouter une vue.
5. Dans la boîte de dialogue Ajouter une vue, vérifiez que le nom de la vue est **supprimer**. Sélectionnez le **créer une vue fortement typée** option et sélectionnez **Album (MvcMusicStore.Models)** à partir de la **classe de modèle** liste déroulante. Sélectionnez **supprimer** à partir de la **modèle de vue de structure** liste déroulante. Les autres champs avec leur valeur par défaut et cliquez sur **ajouter**.

    ![Ajout d’une vue Delete](aspnet-mvc-4-helpers-forms-and-validation/_static/image15.png "Ajout d’une vue de la suppression")

    *Ajout d’une vue de la suppression*
6. Le modèle de suppression affiche tous les champs à partir du modèle. Vous afficherez le titre de l’album uniquement. Pour ce faire, remplacez le contenu de la vue par le code suivant :

    [!code-cshtml[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample16.cshtml)]

<a id="Ex05Task2"></a>

<a id="Task_2_-_Running_the_Application"></a>
#### <a name="task-2---running-the-application"></a>Tâche 2 : exécution de l’Application

Dans cette tâche, vous allez tester que le **StoreManager** **supprimer** afficher la page affiche un formulaire de suppression de confirmation.

1. Appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Modifier l’URL pour **/StoreManager**. Sélectionnez un album à supprimer en cliquant sur **supprimer** et vérifiez que le nouvel affichage est téléchargé.

    ![Suppression d’un Album](aspnet-mvc-4-helpers-forms-and-validation/_static/image16.png "suppression d’un Album")

    *Suppression d’un Album*

<a id="Ex05Task3"></a>

<a id="Task_3-_Implementing_the_HTTP-POST_Delete_Action_Method"></a>
#### <a name="task-3--implementing-the-http-post-delete-action-method"></a>Tâche 3 : implémentation de la méthode d’Action Delete HTTP-POST

Dans cette tâche, vous allez implémenter la version HTTP-POST de la méthode d’action Delete qui sera appelée quand un utilisateur clique sur le **supprimer** bouton. La méthode doit supprimer l’album dans la base de données.

1. Fermez le navigateur si nécessaire, pour revenir à la fenêtre Visual Studio. Ouvrez **StoreManagerController** classe. Pour ce faire, développez le **contrôleurs** et double-cliquez sur **StoreManagerController.cs**.
2. Remplacez **HTTP-POST supprimer** code de méthode d’action avec les éléments suivants :

    (Code d’extrait de code - *Validation - Ex5 gestion suppression HTTP-POST supprimer une action, les formulaires et les programmes d’assistance de ASP.NET MVC 4*)

    [!code-csharp[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample17.cs)]

<a id="Ex5Task4"></a>

<a id="Task_4_-_Running_the_Application"></a>
#### <a name="task-4---running-the-application"></a>Tâche 4 : exécution de l’Application

Dans cette tâche, vous allez tester que le **StoreManager Delete** afficher la page vous permet de supprimer un Album et redirige ensuite à la vue d’Index StoreManager.

1. Appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Modifier l’URL pour **/StoreManager**. Sélectionnez un album à supprimer en cliquant sur **supprimer.** Confirmer la suppression en cliquant sur **supprimer** bouton :

    ![Suppression d’un Album](aspnet-mvc-4-helpers-forms-and-validation/_static/image17.png "suppression d’un Album")

    *Suppression d’un Album*
3. Vérifiez que l’album a été supprimée, car elle n’apparaît pas dans le **Index** page.

<a id="Exercise6"></a>

<a id="Exercise_6_Adding_Validation"></a>
### <a name="exercise-6-adding-validation"></a>Exercice 6 : Ajout de la Validation

Actuellement, les formulaires de créer et modifier en place sans effectuer tout type de validation. Si l’utilisateur quitte un champ obligatoire vide ou des lettres de type dans le champ prix, la première erreur, que vous obtiendrez sera à partir de la base de données.

Vous pouvez ajouter une validation à l’application en ajoutant des Annotations de données à votre classe de modèle. Grâce aux Annotations de données décrivant les règles appliquées aux propriétés de votre modèle, et ASP.NET MVC s’occupe de l’application et afficher le message approprié pour les utilisateurs.

<a id="Ex06Task1"></a>

<a id="Task_1_-_Adding_Data_Annotations"></a>
#### <a name="task-1---adding-data-annotations"></a>Tâche 1 : ajouter des Annotations de données

Dans cette tâche, vous allez ajouter au modèle Album qui permettront à la page Créer et modifier des Annotations de données afficher les messages de validation lorsque cela est approprié.

Pour une classe de modèle simple, ajout d’une Annotation de données est uniquement géré en ajoutant un **à l’aide de** instruction pour **System.ComponentModel.DataAnnotation**, puis placer un **[obligatoire]**attribut sur les propriétés appropriées. L’exemple suivant rendrait la **nom** propriété un champ obligatoire dans la vue.

[!code-csharp[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample18.cs)]

Cela est un peu plus complexe dans le cas de cette application, où le modèle de données d’entité est généré. Si vous avez ajouté des Annotations de données directement dans les classes de modèle, elles sont remplacées si vous mettez à jour le modèle à partir de la base de données. Au lieu de cela, vous pouvez rendre l’utilisation des métadonnées des classes partielles qui existera pour contenir les annotations et sont associés avec le modèle de classes à l’aide la **[MetadataType]** attribut.

1. Ouvrez le **commencer** solution situé dans **Source/Ex6-AddingValidation/début/** dossier. Dans le cas contraire, vous pouvez continuer à utiliser le **fin** solution obtenue par la fin de l’exercice précédent.

    1. Si vous avez ouvert le **commencer** solution, vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Ouvrez le **Album.cs** à partir de la **modèles** dossier.
3. Remplacez **Album.cs** de contenu avec le code en surbrillance, afin qu’il ressemble à ce qui suit :

    > [!NOTE]
    > La ligne **[DisplayFormat(ConvertEmptyStringToNull=false)]** indique que les chaînes vides à partir du modèle ne sont pas être converties en valeurs null lorsque le champ de données est mise à jour dans la source de données. Ce paramètre permet d’éviter une exception lors de l’Entity Framework assigne des valeurs null pour le modèle avant de l’Annotation de données valide les champs.

    (Code d’extrait de code - *programmes d’assistance de ASP.NET MVC 4 et de formulaires et de Validation - classe de métadonnées partielle Ex6 Album*)

    [!code-csharp[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample19.cs)]

    > [!NOTE]
    > Cela **Album** classe partielle a une **MetadataType** attribut qui pointe vers le **AlbumMetaData** classe pour les Annotations de données. Voici les attributs de l’Annotation de données que vous utilisez pour annoter le modèle Album :
    > 
    > - Obligatoire - indique que la propriété est un champ obligatoire
    > - DisplayName - définit le texte doit être utilisé sur les champs de formulaire et des messages de validation
    > - DisplayFormat - Spécifie le mode d’affichage et de mise en forme des champs de données.
    > - StringLength - définit une longueur maximale pour un champ de chaîne
    > - Plage - donne la valeur minimale et maximale pour un champ numérique
    > - ScaffoldColumn - permet de masquer des champs à partir de formulaires de l’éditeur

<a id="Ex06Task2"></a>

<a id="Task_2_-_Running_the_Application"></a>
#### <a name="task-2---running-the-application"></a>Tâche 2 : exécution de l’Application

Dans cette tâche, vous allez tester la validation que les pages créer et modifier des champs, en utilisant les noms d’affichage choisis dans la dernière tâche.

1. Appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Modifier l’URL pour **/StoreManager/créer**. Vérifiez que les noms d’affichage correspondent à celles de la classe partielle (comme **Album Art URL** au lieu de **AlbumArtUrl**)
3. Cliquez sur **créer**, sans le remplir le formulaire. Vérifiez que vous obtenez les messages de validation correspondants.

    ![Validation des champs dans la page Créer](aspnet-mvc-4-helpers-forms-and-validation/_static/image18.png "validé des champs dans la page Créer")

    *Champs validés dans la page de création*
4. Vous pouvez vérifier que le même se produit avec le **modifier** page. Modifier l’URL pour **/StoreManager/Edit/1** et vérifiez que les noms d’affichage correspondent à celles de la classe partielle (comme **Album Art URL** au lieu de **AlbumArtUrl**). Vide le **titre** et **prix** champs et cliquez sur **enregistrer**. Vérifiez que vous obtenez les messages de validation correspondants.

    ![Champs validés dans la page de modification](aspnet-mvc-4-helpers-forms-and-validation/_static/image19.png)

    *Champs validés dans la page de modification*

<a id="Exercise7"></a>

<a id="Exercise_7_Using_Unobtrusive_jQuery_at_Client_Side"></a>
### <a name="exercise-7-using-unobtrusive-jquery-at-client-side"></a>Exercice 7 : À l’aide de jQuery non obstructive côté Client

Dans cet exercice, vous allez apprendre à activer la validation jQuery MVC 4 discrète côté client.

> [!NOTE]
> JQuery non obstructive utilise le préfixe de données-ajax JavaScript pour appeler des méthodes d’action sur le serveur et non intrusive l’émission de scripts clients inline.


<a id="Ex7Task1"></a>

<a id="Task_1_-_Running_the_Application_before_Enabling_Unobtrusive_jQuery"></a>
#### <a name="task-1---running-the-application-before-enabling-unobtrusive-jquery"></a>Tâche 1 : exécution de l’Application avant l’activation discrète jQuery

Dans cette tâche, vous allez exécuter l’application avant l’inclusion de jQuery afin de comparer les deux modèles de validation.

1. Ouvrez le **commencer** solution situé dans **Source/Ex7-UnobtrusivejQueryValidation/début/** dossier. Dans le cas contraire, vous pouvez continuer à utiliser le **fin** solution obtenue par la fin de l’exercice précédent.

    1. Si vous avez ouvert le **commencer** solution, vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Appuyez sur **F5** pour exécuter l’application.
3. Le projet de démarrage dans la page d’accueil. Parcourir **/StoreManager/créer** et cliquez sur **créer** sans le remplir le formulaire pour vérifier que vous obtenez des messages de validation :

    ![Validation du client désactivée](aspnet-mvc-4-helpers-forms-and-validation/_static/image20.png "validation Client désactivée")

    *Validation du client désactivée*
4. Dans le navigateur, ouvrez le code source HTML :

    [!code-html[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample20.html)]

<a id="Ex7Task2"></a>

<a id="Task_2_-_Enabling_Unobtrusive_Client_Validation"></a>
#### <a name="task-2---enabling-unobtrusive-client-validation"></a>Tâche 2 : Validation de Client non obstructive l’activation

Dans cette tâche, vous allez activer jQuery **validation client non obstructive** de **Web.config** fichier, qui est par défaut la valeur false dans tous les nouveaux projets ASP.NET MVC 4. En outre, vous allez ajouter que les références pour rendre le travail de la Validation Client non obstructive de jQuery les scripts nécessaires.

1. Ouvrez **Web.Config** de fichiers à la racine du projet et vous assurer que le **ClientValidationEnabled** et **UnobtrusiveJavaScriptEnabled** clés ont la valeur **true**.

    [!code-xml[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample21.xml)]

    > [!NOTE]
    > Vous pouvez également activer la validation côté client par le code à Global.asax.cs pour obtenir les mêmes résultats :
    > 
    > **HtmlHelper.ClientValidationEnabled = true ;**
    > 
    > En outre, vous pouvez affecter ClientValidationEnabled attribut dans n’importe quel contrôleur d’avoir un comportement personnalisé.
2. Ouvrez **Create.cshtml** à **Views\StoreManager**.
3. Assurez-vous que les fichiers de script suivant **jquery.validate** et **jquery.validate.unobtrusive**, sont référencées dans la vue via la &quot; **~/bundles/jqueryval** &quot; offre groupée.

    [!code-cshtml[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample22.cshtml)]

    > [!NOTE]
    > Toutes ces bibliothèques jQuery sont inclus dans les nouveaux projets de MVC 4. Vous trouverez plus de bibliothèques dans le **/Scripts** dossier de votre projet.
    > 
    > Pour rendre cette validation bibliothèques fonctionnent, vous devez ajouter une référence à la bibliothèque d’infrastructure jQuery. Étant donné que cette référence est déjà ajoutée à la  **\_Layout.cshtml** fichier, vous n’avez pas besoin pour l’ajouter dans cette vue particulière.

<a id="Ex7Task3"></a>

<a id="Task_3_-_Running_the_Application_Using_Unobtrusive_jQuery_Validation"></a>
#### <a name="task-3---running-the-application-using-unobtrusive-jquery-validation"></a>Tâche 3 - Application à l’aide de discrète jQuery Validation en cours d’exécution

Dans cette tâche, vous allez tester que le **StoreManager** créer la vue de modèle effectue la validation côté client à l’aide des bibliothèques de jQuery lorsque l’utilisateur crée un nouvel album.

1. Appuyez sur **F5** pour exécuter l’application.
2. Le projet de démarrage dans la page d’accueil. Parcourir **/StoreManager/créer** et cliquez sur **créer** sans le remplir le formulaire pour vérifier que vous obtenez des messages de validation :

    ![Validation du client avec jQuery activé](aspnet-mvc-4-helpers-forms-and-validation/_static/image21.png "validation Client avec jQuery activé")

    *Validation du client avec jQuery activé*
3. Dans le navigateur, ouvrez le code source pour créer une vue :

    [!code-html[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample23.html)]

    > [!NOTE]
    > Pour chaque règle de validation client, jQuery non obstructive ajoute un attribut avec des données-val -*rulename*=&quot;*message*&quot;. Voici une liste de balises qui discrète jQuery insère dans le champ d’entrée html pour effectuer la validation côté client :
    > 
    > - Val de données
    > - Numéro de données val
    > - Plage de données val
    > - Données val plage min / données-val-range-max
    > - Données val requises
    > - Longueur de données val
    > - Données-val-longueur-max / données val-Longueur-min.
    > 
    > Toutes les valeurs de données sont remplis avec le modèle **Annotation de données**. Vous pouvez ensuite exécuter toute la logique qui fonctionne au côté serveur côté client. Par exemple, des attributs de prix est l’annotation de données suivant dans le modèle :
    > 
    > [!code-csharp[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample24.cs)]
    > 
    > Une fois à l’aide de jQuery non obstructive, le code généré est :
    >  
    > [!code-html[Main](aspnet-mvc-4-helpers-forms-and-validation/samples/sample25.html)]

* * *

<a id="Summary"></a>

<a id="Summary"></a>
## <a name="summary"></a>Résumé

À la fin de cet atelier pratique, vous avez appris comment permettre aux utilisateurs de modifier les données stockées dans la base de données à l’aide des éléments suivants :

- Actions de contrôleur comme Index, créer, modifier, supprimer
- Fonctionnalité de génération de modèles automatique de ASP.NET MVC pour afficher les propriétés d’une table HTML
- Les applications d’assistance HTML personnalisées pour améliorer l’utilisateur expérience
- Méthodes d’action qui réagissent appels HTTP-POST ou HTTP-GET
- Un modèle partagé éditeur pour afficher les modèles similaires, comme créer et modifier
- Les éléments de formulaire tels que les zones déroulantes
- Annotations de données pour la validation de modèle
- Validation côté client à l’aide de la bibliothèque de jQuery non obstructive

<a id="AppendixA"></a>

<a id="Appendix_A_Installing_Visual_Studio_Express_2012_for_Web"></a>
## <a name="appendix-a-installing-visual-studio-express-2012-for-web"></a>Annexe a : installation de Visual Studio Express 2012 pour le Web

Vous pouvez installer **Microsoft Visual Studio Express 2012 pour Web** ou un autre &quot;Express&quot; à l’aide de la version du  **[Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx)** . Les instructions suivantes vous guident à travers les étapes requises pour installer *Visual studio Express 2012 pour le Web* à l’aide de *Microsoft Web Platform Installer*.

1. Accédez à [ [https://go.microsoft.com/?linkid=9810169](https://go.microsoft.com/?linkid=9810169)](https://go.microsoft.com/?linkid=9810169). Sinon, si vous avez déjà installé Web Platform Installer, vous pouvez ouvrir il et recherchez le produit &quot; *Visual Studio Express 2012 pour le Web avec Windows Azure SDK*&quot;.
2. Cliquez sur **installer maintenant**. Si vous n’avez pas **Web Platform Installer** vous allez être redirigé pour télécharger et installer tout d’abord.
3. Une fois **Web Platform Installer** est ouvert, cliquez sur **installer** pour démarrer le programme d’installation.

    ![Installer Visual Studio Express](aspnet-mvc-4-helpers-forms-and-validation/_static/image22.png "installer Visual Studio Express")

    *Installer Visual Studio Express*
4. Lisez les termes et les licences de tous les produits et cliquez sur **J’accepte** pour continuer.

    ![Accepter les termes du contrat de licence](aspnet-mvc-4-helpers-forms-and-validation/_static/image23.png)

    *Accepter les termes du contrat de licence*
5. Attendez que le processus de téléchargement et l’installation se termine.

    ![Progression de l'installation](aspnet-mvc-4-helpers-forms-and-validation/_static/image24.png)

    *Progression de l’installation*
6. Une fois l’installation terminée, cliquez sur **Terminer**.

    ![Installation est terminée](aspnet-mvc-4-helpers-forms-and-validation/_static/image25.png)

    *Installation est terminée*
7. Cliquez sur **Exit** fermer Web Platform Installer.
8. Pour ouvrir Visual Studio Express pour le Web, accédez à la **Démarrer** écran et démarrer l’écriture &quot; **VS Express**&quot;, puis cliquez sur le **Visual Studio Express pour le Web** vignette.

    ![VS Express pour la vignette du Web](aspnet-mvc-4-helpers-forms-and-validation/_static/image26.png)

    *VS Express pour la vignette du Web*

<a id="AppendixB"></a>

<a id="Appendix_B_Using_Code_Snippets"></a>
## <a name="appendix-b-using-code-snippets"></a>Annexe b : à l’aide d’extraits de Code

Avec des extraits de code, vous avez tout le code que vous avez besoin. Le document lab vous indique exactement quand vous pouvez les utiliser, comme indiqué dans l’illustration suivante.

![À l’aide d’extraits de code Visual Studio pour insérer du code dans votre projet](aspnet-mvc-4-helpers-forms-and-validation/_static/image27.png "des extraits de code à l’aide de Visual Studio pour insérer du code dans votre projet")

*À l’aide d’extraits de code Visual Studio pour insérer du code dans votre projet*

***Pour ajouter un extrait de code à l’aide du clavier (c# uniquement)***

1. Placez le curseur où vous souhaitez insérer le code.
2. Commencez à taper le nom de l’extrait de code (sans espaces ou des traits d’union).
3. Observez comment IntelliSense affiche les noms des extraits de code de mise en correspondance.
4. Sélectionnez l’extrait de code correct (ou continuez à taper jusqu'à ce que le nom de l’extrait de code entier est sélectionné).
5. Appuyez sur la touche Tab à deux reprises pour insérer l’extrait de code à l’emplacement du curseur.

![Commencez à taper le nom de l’extrait de code](aspnet-mvc-4-helpers-forms-and-validation/_static/image28.png "commencez à taper le nom de l’extrait de code")

*Commencez à taper le nom de l’extrait de code*

![Appuyez sur Tab pour sélectionner l’extrait de code en surbrillance](aspnet-mvc-4-helpers-forms-and-validation/_static/image29.png "appuyez sur Tab pour sélectionner l’extrait de code en surbrillance")

*Appuyez sur Tab pour sélectionner l’extrait de code en surbrillance*

![Appuyez sur Tab à nouveau et l’extrait de code sont développés](aspnet-mvc-4-helpers-forms-and-validation/_static/image30.png "appuyez sur Tab à nouveau et l’extrait de code seront développe.")

*Appuyez sur Tab à nouveau et l’extrait de code seront développe.*

***Pour ajouter un extrait de code à l’aide de la souris (c#, Visual Basic et XML)*** 1. Clic droit où vous souhaitez insérer l’extrait de code.

1. Sélectionnez **insérer un extrait** suivie **mes extraits de Code**.
2. Sélectionnez l’extrait de code approprié dans la liste, en cliquant dessus.

![Avec le bouton sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait](aspnet-mvc-4-helpers-forms-and-validation/_static/image31.png "avec le bouton sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait")

*Avec le bouton droit sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait*

![Sélectionnez l’extrait de code approprié dans la liste, en cliquant dessus](aspnet-mvc-4-helpers-forms-and-validation/_static/image32.png "choisir l’extrait de code approprié dans la liste, en cliquant sur celle-ci")

*Sélectionnez l’extrait de code approprié dans la liste, en cliquant sur celle-ci*
