---
uid: visual-studio/overview/2013/one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api
title: "Ateliers pratiques : One ASP.NET : intégration ASP.NET Web Forms, MVC et API Web | Documents Microsoft"
author: rick-anderson
description: "ASP.NET est une infrastructure pour la création de sites Web, applications et services à l’aide de technologies spécialisées, telles que MVC, API Web et d’autres. À l’expansion de ASP.NET h..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/16/2014
ms.topic: article
ms.assetid: 4fe2558d-67cc-4d12-a5c1-6fb9f6f16137
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /visual-studio/overview/2013/one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api
msc.type: authoredcontent
ms.openlocfilehash: 55109723e566a9f7c66c1a59414377b05dbec760
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="hands-on-lab-one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api"></a>Ateliers pratiques : One ASP.NET : intégration ASP.NET Web Forms, MVC et API Web
====================
par [Web Camps équipe](https://twitter.com/webcamps)

[Télécharger Camps Web Kit de formation](http://aka.ms/webcamps-training-kit)

> ASP.NET est une infrastructure pour la création de sites Web, applications et services à l’aide de technologies spécialisées, telles que MVC, API Web et d’autres. À l’expansion de ASP.NET a constaté depuis sa création et devez l’avoir ces technologies intégrées, des efforts récentes ont été dans le travail vers **One ASP.NET**.
> 
> Visual Studio 2013 introduit un nouveau système de projet unifiée qui vous permet de créer une application et utiliser toutes les technologies ASP.NET dans un seul projet. Cette fonctionnalité élimine le besoin de choisir une technologie au début d’un projet et le stick avec lui et à la place encourage l’utilisation de plusieurs infrastructures d’ASP.NET au sein d’un projet.
> 
> Tous les exemples de code et des extraits de code sont inclus dans le Kit de formation Camps Web, disponible à l’adresse [http://aka.ms/webcamps-training-kit](http://aka.ms/webcamps-training-kit).


<a id="Overview"></a>
## <a name="overview"></a>Vue d'ensemble

<a id="Objectives"></a>
### <a name="objectives"></a>Objectifs

Dans cet atelier pratique, vous allez apprendre comment :

- Créer un site Web basé sur le **One ASP.NET** type de projet
- Utilisez différents **ASP.NET** comme **MVC** et **API Web** dans le même projet
- Identifier les principaux composants d’un **ASP.NET** application
- Tirer parti de la **génération de modèles automatique ASP.NET** framework pour créer automatiquement les contrôleurs et les vues pour exécuter des opérations CRUD basé sur vos classes de modèle
- Exposer le même ensemble d’informations dans des formats machine - et explicite à l’aide de l’outil approprié pour chaque tâche

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Conditions préalables

Les éléments suivants sont nécessaire pour terminer cet atelier pratique :

- [Visual Studio Express 2013 pour le Web](https://www.microsoft.com/visualstudio/) ou supérieur
- [Visual Studio 2013 Update 1](https://go.microsoft.com/fwlink/?LinkId=301714)

<a id="Setup"></a>
### <a name="setup"></a>Installation

Pour exécuter les exercices de cet atelier, vous devez configurer votre environnement tout d’abord.

1. Ouvrez l’Explorateur Windows et accédez à de l’atelier **Source** dossier.
2. Avec le bouton droit sur **Setup.cmd** et sélectionnez **exécuter en tant qu’administrateur** pour lancer le processus d’installation qui sera configurer votre environnement et installer les extraits de code Visual Studio pour ce laboratoire.
3. Si la boîte de dialogue contrôle de compte d’utilisateur s’affiche, confirmez l’action pour continuer.

> [!NOTE]
> Assurez-vous que vous avez activé toutes les dépendances pour ce laboratoire avant d’exécuter le programme d’installation.


<a id="CodeSnippets"></a>
### <a name="using-the-code-snippets"></a>À l’aide d’extraits de Code

Dans le document de laboratoire, vous serez invité à insérer des blocs de code. Pour des raisons pratiques, la majeure partie de ce code est fourni en tant que Visual Studio extraits de Code, vous pouvez accéder à partir de Visual Studio 2013 pour éviter d’avoir à ajouter manuellement.

> [!NOTE]
> Chaque exercice est accompagnée d’une solution de départ située dans le **commencer** dossier de l’exercice qui vous permet de suivre chaque exercice indépendamment des autres. Sachez que les extraits de code sont ajoutés au cours d’un exercice sont manquants à partir de ces solutions de démarrage et peut ne pas fonctionneront tant que vous avez terminé l’exercice. Dans le code source pour un exercice, vous trouverez également une **fin** dossier qui contient une solution Visual Studio avec le code qui résulte de la procédure dans l’exercice correspondant. Si vous avez besoin d’aide au cours de cet atelier, vous pouvez utiliser ces solutions comme guide.


* * *

<a id="Exercises"></a>
## <a name="exercises"></a>Exercices

Cet atelier pratique inclut les exercices suivants :

1. [Création d’un projet Web Forms](#Exercise1)
2. [Création d’un contrôleur MVC à l’aide de la génération de modèles automatique](#Exercise2)
3. [Création d’un contrôleur d’API Web à l’aide de la génération de modèles automatique](#Exercise3)

Durée estimée pour effectuer ce laboratoire : **60 minutes**

> [!NOTE]
> Lorsque vous démarrez Visual Studio, vous devez sélectionner une des collections de paramètres prédéfinis. Chaque collection prédéfinie est conçue pour faire correspondre un style de développement particulier et détermine les dispositions de fenêtres, le comportement de l’éditeur, extraits de code IntelliSense et les options de boîte de dialogue. Les procédures de cet atelier décrivent les actions nécessaires pour accomplir une tâche donnée dans Visual Studio lorsque vous utilisez la **paramètres de développement généraux** collection. Si vous choisissez une collection de paramètres différents pour votre environnement de développement, il est possible les différences dans les étapes que vous devez prendre en compte.


<a id="Exercise1"></a>
### <a name="exercise-1-creating-a-new-web-forms-project"></a>Exercice 1 : Création d’un projet Web Forms

Dans cet exercice, vous allez créer un nouveau site Web Forms dans Visual Studio 2013 à l’aide de la **One ASP.NET** unifiée expérience de projet, ce qui vous permet de s’intégrer facilement des composants Web Forms, MVC et API Web dans la même application. Vous allez ensuite Explorer la solution générée et identifier ses parties, et enfin, vous verrez le site Web dans l’action.

<a id="Ex1Task1"></a>
#### <a name="task-1--creating-a-new-site-using-the-one-aspnet-experience"></a>Tâche 1 : création d’un Site à l’aide de l’une expérience d’ASP.NET

Dans cette tâche, vous allez démarrer la création d’un nouveau site Web dans Visual Studio basé sur le **One ASP.NET** type de projet. **Un ASP.NET** unifie toutes les technologies ASP.NET et vous permet de combiner et de les mettre en correspondance comme vous le souhaitez. Puis facile à reconnaître les différents composants de Web Forms, MVC et API Web live côte à côte dans votre application.

1. Ouvrez **Visual Studio Express 2013 pour le Web** et sélectionnez **fichier | Nouveau projet...**  pour démarrer une nouvelle solution.

    ![Création d'un projet](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image1.png)

    *Création d’un projet*
2. Dans le **nouveau projet** boîte de dialogue, sélectionnez **Application Web ASP.NET** sous le **Visual C# | Web** onglet et vérifiez que **.NET Framework 4.5** est sélectionnée. Nommez le projet *MyHybridSite*, choisissez un **emplacement** et cliquez sur **OK**.

    ![Nouveau projet d’Application Web ASP.NET](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image2.png)

    *Création d’un projet d’Application Web ASP.NET*
3. Dans le **nouveau projet ASP.NET** boîte de dialogue, sélectionnez le **Web Forms** modèle et sélectionnez le **MVC** et **API Web** options. En outre, assurez-vous que le **authentification** option est définie sur **comptes d’utilisateur individuels**. Cliquez sur **OK** pour continuer.

    ![Création d’un projet avec le modèle Web Forms, y compris les composants Web API et MVC](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image3.png)

    *Création d’un projet avec le modèle Web Forms, y compris les composants Web API et MVC*
4. Vous pouvez maintenant Explorer la structure de la solution générée.

    ![Exploration de la solution générée](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image4.png)

    *Exploration de la solution générée*

    1. **Compte :** ce dossier contient les pages de formulaire Web pour vous inscrire, connectez-vous à et gérer les comptes d’utilisateur de l’application. Ce dossier est ajouté quand le **comptes d’utilisateur individuels** option d’authentification est sélectionnée lors de la configuration du modèle de projet Web Forms.
    2. **Modèles :** ce dossier contient les classes qui représentent les données de vos applications.
    3. **Contrôleurs** et **vues**: ces dossiers sont requis pour le **ASP.NET MVC** et **API Web ASP.NET** composants. Vous allez explorer les technologies MVC et les API Web dans les exercices suivants.
    4. Le **Default.aspx**, **Contact.aspx** et **About.aspx** fichiers sont des pages Web Form prédéfinies que vous pouvez utiliser comme points de départ pour générer les pages spécifiques à votre application. La logique de programmation de ces fichiers se trouve dans un fichier distinct appelé le &quot;code-behind&quot; fichier, qui a un &quot;. aspx.vb&quot; ou &quot;. aspx.cs&quot; extension (en fonction de la langage utilisé). La logique de code-behind s’exécute sur le serveur et génère dynamiquement la sortie HTML de votre page.
    5. Le **Site.Master** et **Site.Mobile.Master** pages définissent l’apparence et le comportement standard de toutes les pages dans l’application.
5. Double-cliquez sur le **Default.aspx** fichier pour Explorer le contenu de la page.

    ![Exploration de la page Default.aspx](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image5.png)

    *Exploration de la page Default.aspx*

    > [!NOTE]
    > Le **Page** directive en haut du fichier définit les attributs de la page Web Forms. Par exemple, le **MasterPageFile** attribut spécifie le chemin d’accès au contrôleur de page - dans ce cas, le *Site.Master* page - et **Inherits** attribut définit le classe code-behind pour la page à hériter. Cette classe se trouve dans le fichier déterminé par le **code-behind** attribut.
    > 
    > Le **asp : Content** contrôle contient le contenu réel de la page (texte, balisage et contrôles) et est mappé à un **asp : ContentPlaceHolder** contrôle sur la page maître. Dans ce cas, le contenu de la page est rendu dans le *MainContent* contrôle défini dans le *Site.Master* page.
6. Développez le **application\_Démarrer** dossier et notez la **WebApiConfig.cs** fichier. Visual Studio inclus ce fichier dans la solution générée, car vous avez inclus les API Web lors de la configuration de votre projet avec le modèle One ASP.NET.
7. Ouvrez le **WebApiConfig.cs** fichier. Dans le *WebApiConfig* classe, vous trouverez la configuration associée aux API Web, qui mappe HTTP achemine vers **contrôleurs de l’API Web**.

    [!code-csharp[Main](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/samples/sample1.cs)]
8. Ouvrez le **RouteConfig.cs** fichier. À l’intérieur de la *RegisterRoutes* méthode vous trouverez la configuration associée MVC, qui mappe les itinéraires HTTP à **contrôleurs MVC**.

    [!code-csharp[Main](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/samples/sample2.cs)]

<a id="Ex1Task2"></a>
#### <a name="task-2--running-the-solution"></a>Tâche 2 : exécution de la Solution

Dans cette tâche, vous allez exécuter la solution générée, explorez l’application et certaines de ses fonctionnalités, telles que la réécriture d’URL et l’authentification intégrée.

1. Pour exécuter la solution, appuyez sur **F5** ou cliquez sur le **Démarrer** bouton situé sur la barre d’outils. Page d’accueil de l’application doit ouvrir dans le navigateur.

    ![Exécution de la solution](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image6.png)
2. Vérifiez que les pages Web Forms sont appelées. Pour ce faire, ajoutez **/contact.aspx** à l’URL dans la barre d’adresses et appuyez sur **entrée**.

    ![URL conviviales](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image7.png)

    *URL conviviales*

    > [!NOTE]
    > Comme vous pouvez le voir, l’URL devient **/contactez**. À partir **ASP.NET 4**, les fonctionnalités de routage d’URL ont été ajoutées aux Web Forms, afin d’écrire des URL comme  *[http://www.mysite.com/products/software](http://www.mysite.com/products/software)*  au lieu de  *[http://www.mysite.com/products.aspx?category=software](http://www.mysite.com/products.aspx?category=software)*. Pour plus d’informations, reportez-vous à [routage d’URL](../../../web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/url-routing.md).
3. Vous allez maintenant découvrir le flux d’authentification intégré dans l’application. Pour ce faire, cliquez sur **enregistrer** dans le coin supérieur droit de la page.

    ![L’inscription d’un nouvel utilisateur](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image8.png)

    *L’inscription d’un nouvel utilisateur*
4. Dans le **inscrire** , entrez un **nom d’utilisateur** et **mot de passe**, puis cliquez sur **inscrire**.

    ![Page d’inscription](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image9.png)

    *Page d’inscription*
5. L’application enregistre le nouveau compte, et l’utilisateur est authentifié.

    ![Utilisateur authentifié](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image10.png)

    *Utilisateur authentifié*
6. Revenez à Visual Studio, puis appuyez sur **MAJ + F5** pour arrêter le débogage.

<a id="Exercise2"></a>
### <a name="exercise-2-creating-an-mvc-controller-using-scaffolding"></a>Exercice 2 : Création d’un contrôleur MVC à l’aide de la génération de modèles automatique

Dans cet exercice vous tirera parti de l’infrastructure de génération de modèles automatique ASP.NET fourni par Visual Studio pour créer un contrôleur ASP.NET MVC 5 avec des actions et vues Razor permettant d’effectuer des opérations CRUD, sans écrire une seule ligne de code. Le processus de génération de modèles automatique utilise Entity Framework Code First pour générer le contexte de données et le schéma de base de données dans la base de données SQL.

**À propos d’Entity Framework Code First**

Entity Framework (EF) est un mappeur objet / relationnel (ORM) qui vous permet de créer des applications d’accès aux données par programmation avec un modèle d’application conceptuel plutôt que directement à l’aide d’un schéma de stockage relationnel.

Le flux de travail de modélisation Entity Framework Code First vous permet d’utiliser vos propres classes de domaine pour représenter le modèle EF s’appuie sur lors de l’exécution de requêtes, les fonctions de suivi des modifications et mise à jour. Utilisant le workflow de développement Code First, vous n’avez pas besoin commencer votre application en créant une base de données ou de spécification d’un schéma. Au lieu de cela, vous pouvez écrire des classes .NET standard qui définissent les objets de modèle de domaine plus appropriés pour votre application, et Entity Framework crée la base de données pour vous.

> [!NOTE]
> Plus d’informations sur Entity Framework [ici](../../../entity-framework.md).


<a id="Ex2Task1"></a>
#### <a name="task-1--creating-a-new-model"></a>Tâche 1 : création d’un modèle

Vous allez maintenant définir un **personne** (classe), qui sera le modèle utilisé par le processus de génération de modèles automatique pour créer le contrôleur MVC et les vues. Vous allez commencer par créer un **personne** classe de modèle et les opérations CRUD dans le contrôleur seront automatiquement créé à l’aide des fonctionnalités de génération de modèles automatique.

1. Ouvrez **Visual Studio Express 2013 pour le Web** et le **MyHybridSite.sln** solution situé dans le **Source/Ex2-MvcScaffolding/début** dossier. Ou bien, vous pouvez continuer avec la solution que vous avez obtenu dans l’exercice précédent.
2. Dans **l’Explorateur de solutions**, avec le bouton droit le **modèles** dossier de la **MyHybridSite** de projet et sélectionnez **ajouter | Classe...** .

    ![Ajout de la classe de modèle personne](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image11.png)

    *Ajout de la classe de modèle personne*
3. Dans le **ajouter un nouvel élément** boîte de dialogue, nommez le fichier *Person.cs* et cliquez sur **ajouter**.

    ![Création de la classe de modèle personne](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image12.png)

    *Création de la classe de modèle personne*
4. Remplacez le contenu de la **Person.cs** fichier avec le code suivant. Appuyez sur **CTRL + S** pour enregistrer les modifications.

    (Code d’extrait de code - *BringingTogetherOneAspNet - Ex2 - PersonClass*)

    [!code-csharp[Main](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/samples/sample3.cs)]
5. Dans **l’Explorateur de solutions**, avec le bouton droit le **MyHybridSite** de projet et sélectionnez **générer**, ou appuyez sur **CTRL + MAJ + B** pour générer le projet.

<a id="Ex2Task2"></a>
#### <a name="task-2--creating-an-mvc-controller"></a>Tâche 2 : création d’un contrôleur MVC

Maintenant que le **personne** modèle est créé, vous allez utiliser la génération de modèles automatique ASP.NET MVC avec Entity Framework pour créer les actions de contrôleur CRUD et les vues pour **personne**.

1. Dans **l’Explorateur de solutions**, avec le bouton droit le **contrôleurs** dossier de la **MyHybridSite** de projet et sélectionnez **ajouter | Nouvel élément structuré...** .

    ![Création d’un nouveau contrôleur de modèle généré automatiquement](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image13.png)

    *Création d’un nouveau contrôleur structuré*
2. Dans le **ajouter une vue de structure** boîte de dialogue, sélectionnez **contrôleur MVC 5 avec vues, utilisant Entity Framework** puis cliquez sur **ajouter.**

    ![Sélection du contrôleur MVC 5 avec des vues et Entity Framework](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image14.png)

    *Sélection du contrôleur MVC 5 avec des vues et Entity Framework*
3. Définir *MvcPersonController* en tant que le **nom du contrôleur**, sélectionnez le **utiliser les actions de contrôleur asynchrones** option et sélectionnez **personne (MyHybridSite.Models)**  comme le **classe de modèle**.

    ![Ajout d’un contrôleur MVC avec la génération de modèles automatique](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image15.png)

    *Ajout d’un contrôleur MVC avec la génération de modèles automatique*
4. Sous **classe de contexte de données**, cliquez sur **nouveau contexte de données...** .

    ![Création d’un nouveau contexte de données](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image16.png)

    *Création d’un nouveau contexte de données*
5. Dans le **nouveau contexte de données** boîte de dialogue, le nom du nouveau contexte de données *PersonContext* et cliquez sur **ajouter**.

    ![Création de la nouvelle PersonContext](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image17.png)

    *Création du nouveau type de PersonContext*
6. Cliquez sur **ajouter** pour créer le nouveau contrôleur de **personne** avec la génération de modèles automatique. Visual Studio génère ensuite les actions de contrôleur, le contexte de données de personne et les vues Razor.

    ![Après avoir créé le contrôleur MVC avec la génération de modèles automatique](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image18.png)

    *Après avoir créé le contrôleur MVC avec la génération de modèles automatique*
7. Ouvrez le **MvcPersonController.cs** de fichiers dans le **contrôleurs** dossier. Notez que les méthodes d’action CRUD ont été générées automatiquement.

    [!code-csharp[Main](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/samples/sample4.cs)]

    > [!NOTE]
    > En sélectionnant le **utiliser les actions de contrôleur asynchrones** options de case à cocher de la génération de modèles automatique dans les étapes précédentes, Visual Studio génère des méthodes d’action asynchrones pour toutes les actions qui impliquent l’accès au contexte de données Person. Il est recommandé que vous utilisez des méthodes d’action asynchrones pour longue, UC non lié demandes pour éviter de bloquer le serveur Web de réaliser un travail pendant le traitement de la demande.

<a id="Ex2Task3"></a>
#### <a name="task-3--running-the-solution"></a>Tâche 3 : exécution de la Solution

Dans cette tâche, vous allez exécuter la solution pour vous assurer que les vues pour **personne** fonctionnent comme prévu. Vous allez ajouter une nouvelle personne pour vérifier qu’il est correctement enregistré dans la base de données.

1. Appuyez sur **F5** pour exécuter la solution.
2. Accédez à **/MvcPerson**. La vue de modèle généré automatiquement qui présente la liste des personnes doit apparaître.
3. Cliquez sur **créer un nouveau** pour ajouter une nouvelle personne.

    ![Accéder aux vues MVC modèle généré automatiquement](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image19.png)

    *Accéder aux vues MVC modèle généré automatiquement*
4. Dans le **créer** afficher, fournissez un **nom** et un **âge** pour la personne, puis cliquez sur **créer**.

    ![Ajout d’une nouvelle personne](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image20.png)

    *Ajout d’une nouvelle personne*
5. La nouvelle personne est ajoutée à la liste. Dans la liste d’éléments, cliquez sur **détails** pour afficher les détails de la personne. Puis, dans le **détails** afficher, cliquez sur **retour à la liste** pour revenir à la vue liste.

    ![Afficher les détails de la personne](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image21.png)

    *Afficher les détails de la personne*
6. Cliquez sur le **supprimer** lien à supprimer de la personne. Dans le **supprimer** afficher, cliquez sur **supprimer** à confirmer l’opération.

    ![La suppression d’une personne](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image22.png)

    *La suppression d’une personne*
7. Revenez à Visual Studio, puis appuyez sur **MAJ + F5** pour arrêter le débogage.

<a id="Exercise3"></a>
### <a name="exercise-3-creating-a-web-api-controller-using-scaffolding"></a>Exercice 3 : Création d’un contrôleur d’API Web à l’aide de la génération de modèles automatique

L’infrastructure API Web fait partie de la pile ASP.NET et conçu pour faciliter la mise en œuvre des services HTTP, généralement envoyer et recevoir des données JSON ou XML via une API RESTful.

Dans cet exercice, vous utiliserez la structure de ASP.NET à nouveau pour générer un contrôleur d’API Web. Vous allez utiliser le même **personne** et **PersonContext** classes à partir de l’exercice précédent pour fournir les mêmes données person au format JSON. Vous verrez comment vous pouvez exposer les mêmes ressources de plusieurs manières dans l’application ASP.NET.

<a id="Ex3Task1"></a>
#### <a name="task-1--creating-a-web-api-controller"></a>Tâche 1 : création d’un contrôleur d’API Web

Dans cette tâche, vous allez créer un nouveau **contrôleur d’API Web** qui expose les données de la personne dans un format utilisables par des ordinateurs tels que JSON.

1. Si pas déjà ouvert, ouvrez **Visual Studio Express 2013 pour le Web** et ouvrez le **MyHybridSite.sln** solution situé dans le **début/WebAPI-Ex3/Source** dossier. Ou bien, vous pouvez continuer avec la solution que vous avez obtenu dans l’exercice précédent.

    > [!NOTE]
    > Si vous commencez avec la solution de début à partir de l’exercice 3, appuyez sur **CTRL + MAJ + B** pour générer la solution.
2. Dans **l’Explorateur de solutions**, avec le bouton droit le **contrôleurs** dossier de la **MyHybridSite** de projet et sélectionnez **ajouter | Nouvel élément structuré...** .

    ![Création d’un nouveau contrôleur de modèle généré automatiquement](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image23.png)

    *Création d’un nouveau contrôleur de modèle généré automatiquement*
3. Dans le **ajouter une vue de structure** boîte de dialogue, sélectionnez **API Web** dans le volet gauche, puis **Web API 2 contrôleur avec actions, utilisant Entity Framework** dans le volet central, puis  **Ajouter.**

    ![Sélection du contrôleur de Web API 2 avec Entity Framework et les actions](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image24.png "en sélectionnant des contrôleur Web API 2 avec Entity Framework et les actions")

    *Sélection du contrôleur de Web API 2 avec Entity Framework et les actions*
4. Définir *ApiPersonController* en tant que le **nom du contrôleur**, sélectionnez le **utiliser les actions de contrôleur asynchrones** option et sélectionnez **personne (MyHybridSite.Models)**  et **PersonContext (MyHybridSite.Models)** comme le **modèle** et **contexte de données** classes respectivement. Cliquez ensuite sur **Ajouter**.

    ![Ajout d’un contrôleur d’API Web avec la structure](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image25.png "Ajout d’un contrôleur d’API Web avec la génération de modèles automatique")

    *Ajout d’un contrôleur d’API Web avec la génération de modèles automatique*
5. Visual Studio génère ensuite le **ApiPersonController** classe avec les quatre actions CRUD pour travailler avec vos données.

    ![Après avoir créé le contrôleur d’API Web avec la structure](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image26.png "après avoir créé le contrôleur d’API Web avec la génération de modèles automatique")

    *Après avoir créé le contrôleur d’API Web avec la génération de modèles automatique*
6. Ouvrez le **ApiPersonController.cs** de fichiers et d’inspecter le *GetPeople* méthode d’action. Cette méthode interroge le champ de base de données de **PersonContext** type afin d’obtenir des données de personnes.

    [!code-csharp[Main](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/samples/sample5.cs)]
7. Vous remarquez que le commentaire au-dessus de la définition de méthode. Il fournit l’URI qui expose cette action que vous utiliserez dans la tâche suivante.

    [!code-csharp[Main](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/samples/sample6.cs)]

    > [!NOTE]
    > Par défaut, les API Web est configuré pour intercepter les requêtes pour le */API* chemin d’accès pour éviter les conflits avec les contrôleurs MVC. Si vous devez modifier ce paramètre, reportez-vous à [le routage ASP.NET Web API](../../../web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api.md).

<a id="Ex3Task2"></a>
#### <a name="task-2--running-the-solution"></a>Tâche 2 : exécution de la Solution

Dans cette tâche, vous allez utiliser Internet Explorer **outils de développement F12** pour inspecter la réponse complète à partir du contrôleur d’API Web. Vous verrez comment vous pouvez capturer le trafic réseau pour obtenir plus de détails sur vos données d’application.

> [!NOTE]
> Assurez-vous que **Internet Explorer** est sélectionné dans le **Démarrer** bouton situé sur la barre d’outils de Visual Studio.
> 
> ![Option d’Internet Explorer](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image27.png)
> 
> Le **outils de développement F12** ont un large éventail de fonctionnalités qui ne sont pas couverte dans ce exercices laboratoire. Si vous souhaitez plus d’informations, reportez-vous à [utilisant les outils de développement F12](https://msdn.microsoft.com/library/ie/bg182326(v=vs.85)).


1. Appuyez sur **F5** pour exécuter la solution.

    > [!NOTE]
    > Pour effectuer cette tâche correctement, votre application doit avoir des données. Si votre base de données est vide, vous pouvez revenir en arrière à la tâche 3 dans l’exercice 2 et suivez les étapes de la création d’une nouvelle personne à l’aide de vues MVC.
2. Dans le navigateur, appuyez sur **F12** pour ouvrir le **outils de développement** Panneau de configuration. Appuyez sur **CTRL** + **4** ou cliquez sur le **réseau** icône, puis cliquez sur la flèche verte pour commencer la capture du trafic réseau.

    ![Initialisation de capture de réseau d’API Web](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image28.png "capture réseau de lancement des API Web")

    *Initialisation de capture de réseau d’API Web*
3. Ajouter **api/ApiPerson** à l’URL dans la barre d’adresses du navigateur. Vous inspecte maintenant les détails de la réponse de la **ApiPersonController**.

    ![La récupération de données via l’API Web](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image29.png "la récupération de données via l’API Web")

    *La récupération des données via l’API Web*

    > [!NOTE]
    > Une fois le téléchargement terminé, vous devrez effectuer une action avec le fichier téléchargé. Laissez la boîte de dialogue ouverte pour pouvoir être en mesure d’observer le contenu de réponse via la fenêtre outil de développeurs.
4. Maintenant vous inspecte le corps de la réponse. Pour ce faire, cliquez sur le **détails** onglet, puis cliquez sur **corps de réponse**. Vous pouvez vérifier que les données téléchargées sont une liste d’objets avec les propriétés **Id**, **nom** et **âge** qui correspondent à la **personne** classe.

    ![Affichage Web de corps de la réponse de l’API](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image30.png "affichage Web des corps de la réponse de l’API")

    *Corps de réponse de l’API Web de visualisation*

<a id="Ex3Task3"></a>
#### <a name="task-3--adding-web-api-help-pages"></a>Tâche 3 : ajout de Pages de l’aide API Web

Lorsque vous créez une API Web, il est utile de créer une page d’aide afin que les autres développeurs sache comment appeler votre API. Vous pouvez créer et mettre à jour manuellement les pages de la documentation, mais il est préférable de générer automatiquement pour éviter d’avoir à effectuer les tâches de maintenance. Dans cette tâche, vous utiliserez un package Nuget pour générer automatiquement des pages d’aide API Web à la solution.

1. À partir de la **outils** menu dans Visual Studio, sélectionnez **Gestionnaire de Package de bibliothèque**, puis cliquez sur **Package Manager Console**.
2. Dans le **Package Manager Console** fenêtre, exécutez la commande suivante :

    [!code-powershell[Main](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/samples/sample7.ps1)]

    > [!NOTE]
    > Le **Microsoft.AspNet.WebApi.HelpPage** package installe les assemblys nécessaires et ajoute des vues MVC pour les pages d’aide sous le **zones/HelpPage** dossier.

    ![Zone de HelpPage](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image31.png "HelpPage zone")

    *Zone de HelpPage*
3. Par défaut, l’aide de pages ont des chaînes d’espace réservé pour la documentation. Vous pouvez utiliser des commentaires de documentation XML pour créer la documentation. Pour activer cette fonctionnalité, ouvrez le **HelpPageConfig.cs** fichier situé dans le **zones/HelpPage/application\_Démarrer** dossier et ne commentez pas la ligne suivante :

    [!code-javascript[Main](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/samples/sample8.js)]
4. Dans **l’Explorateur de solutions**, cliquez sur le projet **MyHybridSite**, sélectionnez **propriétés** et cliquez sur le **générer** onglet.

    ![Onglet Générer](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image32.png "générer la section")

    *Onglet Générer*
5. Sous **sortie**, sélectionnez **fichier de documentation XML**. Dans la zone d’édition, tapez **application\_Data/XmlDocument.xml**.

    ![Section dans l’onglet générer de sortie](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image33.png "section dans l’onglet générer de sortie")

    *Section de sortie dans l’onglet Générer*
6. Appuyez sur **CTRL** + **S** pour enregistrer les modifications.
7. Ouvrir le **ApiPersonController.cs** de fichiers à partir de la **contrôleurs** dossier.
8. Entrez une nouvelle ligne entre les *GetPeople* signature de méthode et la */ / api/ApiPerson d’obtenir* de commentaire, puis tapez les trois barres obliques.

    > [!NOTE]
    > Visual Studio insère automatiquement les éléments XML qui définissent la documentation de la méthode.
9. Ajouter un texte de résumé et de la valeur de retour pour la *GetPeople* (méthode). Il doit se présenter comme suit.

    [!code-csharp[Main](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/samples/sample9.cs)]
10. Appuyez sur **F5** pour exécuter la solution.
11. Ajouter **/Help** à l’URL dans la barre d’adresses pour accéder à la page d’aide.

    ![Page d’aide de l’API Web ASP.NET](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image34.png "Page d’aide de l’API Web ASP.NET")

    *Page d’aide de l’API Web ASP.NET*

    > [!NOTE]
    > Le contenu principal de la page est une table d’API, regroupés par contrôleur. Les entrées de table sont générées dynamiquement, à l’aide de la **IApiExplorer** interface. Si vous ajoutez ou mettez à jour le contrôleur d’API, la table mettra à jour automatiquement la prochaine fois que vous générez l’application.
    > 
    > Le **API** colonne répertorie la méthode HTTP et un URI relatif. Le **Description** colonne contienne des informations qui ont été extraites de la documentation de la méthode.
12. Notez que la description que vous avez ajouté au-dessus de la définition de méthode s’affiche dans la colonne description.

    ![Description de la méthode API](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image35.png "description de la méthode API")

    *Description de la méthode API*
13. Cliquez sur une des méthodes API pour accéder à une page avec des informations plus détaillées, y compris le corps de réponse d’exemple.

    ![Page d’informations de détail](one-aspnet-integrating-aspnet-web-forms-mvc-and-web-api/_static/image36.png "page d’informations sur les détails")

    *Page d’informations détaillées*

* * *

<a id="Summary"></a>
## <a name="summary"></a>Résumé

À la fin de cet atelier pratique, vous avez appris comment :

- Créez une application Web à l’aide de l’une expérience d’ASP.NET dans Visual Studio 2013
- Intégrer plusieurs technologies ASP.NET dans un seul projet
- Générer des contrôleurs MVC et les vues à partir de vos classes de modèle à l’aide de la génération de modèles automatique ASP.NET
- Générer des contrôleurs de l’API Web, qui utilisent des fonctionnalités telles que la programmation asynchrone et d’accès aux données via Entity Framework
- Générer automatiquement des Pages d’aide API Web pour vos contrôleurs
