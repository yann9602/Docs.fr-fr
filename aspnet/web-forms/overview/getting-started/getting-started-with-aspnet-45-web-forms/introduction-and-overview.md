---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
title: Prise en main 4.5 Web Forms ASP.NET et Visual Studio 2013 | Documents Microsoft
author: Erikre
description: "Cette série de didacticiels pas à pas, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/08/2014
ms.topic: article
ms.assetid: 9b96eaa1-8ef0-4338-a2e8-e0f970bfaf68
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
msc.type: authoredcontent
ms.openlocfilehash: 6ae398f94c0ac3872601bdc8fd935f6d285793db
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="getting-started-with-aspnet-45-web-forms-and-visual-studio-2013"></a>Prise en main 4.5 Web Forms ASP.NET et Visual Studio 2013
====================
Par [Erik Reitan](https://github.com/Erikre)

[Télécharger Wingtip Toys exemple de projet (c#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) ou [télécharger des livres (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)

> Cette série de didacticiels pas à pas, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour le Web. [Web Forms ASP.NET questionnaire](http://quizapp.cloudapp.net/?quiz=ASP.NET)  
> Testez vos connaissances et approfondir les concepts clés en prenant le questionnaire d’ASP.NET Web Forms. Ce test a été spécialement conçu de contenu de cette série de didacticiels. Chaque question dans le questionnaire fournit une explication, ainsi que des liens vers des informations supplémentaires.


## <a name="introduction"></a>Introduction

Cette série de didacticiels vous guide à travers les étapes requises pour créer une application Web Forms ASP.NET à l’aide de Visual Studio Express 2013 pour le Web et ASP.NET 4.5.

L’application que vous allez créer est nommée **Wingtip Toys**. Il s’agit d’un exemple simplifié d’un site web avant de magasin qui vend des éléments en ligne. Cette série de didacticiels met en évidence les nouvelles fonctionnalités disponibles dans ASP.NET 4.5.

Commentaires sont les bienvenus et nous allons effectuer tout en oeuvre pour mettre à jour cette série de didacticiels en fonction de vos suggestions.

### <a name="download-completed-project"></a>Projet de téléchargement terminé

Vous pouvez télécharger un projet c# qui contient le didacticiel terminé.

- [Prise en main 4.5 Web Forms ASP.NET et Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (c#)

### <a name="review-the-content-by-taking-the-related-aspnet-web-forms-quiz"></a>Passez en revue le contenu en prenant le questionnaire ASP.NET Web Forms connexe

Après avoir terminé ce didacticiel, testez vos connaissances et approfondir les concepts clés en prenant le [ASP.NET Web Forms questionnaire](http://quizapp.cloudapp.net/?quiz=ASP.NET&cdn_id=2014-01-17-001). Ce test a été spécialement conçu de contenu de cette série de didacticiels. Chaque question dans le questionnaire fournit une explication, ainsi que des liens vers des informations supplémentaires.

- [Web Forms ASP.NET questionnaire](http://quizapp.cloudapp.net/?quiz=ASP.NET)

### <a name="audience"></a>Public

Est de cette série de didacticiels destiné aux développeurs expérimentés qui sont nouveaux pour ASP.NET Web Forms. Un développeur intéressé par cette série de didacticiels doit avoir les compétences suivantes :

- Un objet qui connaissent orientée langage de programmation (OOP)
- Familiarisé avec les concepts de développement Web (HTML, CSS, JavaScript)
- Familiarisé avec les concepts de base de données relationnelle
- Familiarisé avec les concepts de l’architecture multicouche

Si vous souhaitez parcourir les éléments mentionnés ci-dessus, examinez le contenu suivant :

- [Prise en main de Visual c#](https://msdn.microsoft.com/library/a72418yk.aspx)
- [Web Development](https://msdn.microsoft.com/beginner/bb308760.aspx), [HTML, CSS, JavaScript, SQL, PHP, JQuery](http://w3schools.com/)
- [Base de données relationnelle](http://en.wikipedia.org/wiki/Relational_database)
- [Architecture multiniveau](http://en.wikipedia.org/wiki/Multitier_architecture)

### <a name="application-features"></a>Fonctionnalités de l’application

Les fonctionnalités d’ASP.NET Web Form présentées dans cette série sont les suivantes :

- Le projet d’Application Web (pas le projet de Site Web)
- Web Forms
- Pages maîtres, Configuration
- programme d’amorçage
- Entity Framework Code First, base de données locale
- Validation de la demande
- Fortement typé, les contrôles de données de modèle de liaison, les Annotations de données et que la valeur de fournisseurs
- Le protocole SSL et OAuth
- Identité ASP.NET, Configuration et autorisation
- Validation non obstrusive
- Routage
- Gestion des erreurs de ASP.NET

### <a name="application-scenarios-and-tasks"></a>Tâches et les scénarios d’application

Les tâches décrites dans cette série sont les suivantes :

- Création, la révision et le nouveau projet en cours d’exécution
- Création de la structure de base de données
- L’initialisation et l’amorçage de la base de données
- Personnalisation de l’interface utilisateur à l’aide de styles, de graphiques et d’une page maître
- Ajout de pages et de navigation
- Affichage des détails de menu et les données de produit
- Création d’un panier d’achat
- Prise en charge SSL Ajout et OAuth
- Ajout d’une méthode de paiement
- Y compris un rôle d’administrateur et un utilisateur à l’application
- Restreindre l’accès à des pages spécifiques et de dossier
- Téléchargement d’un fichier à l’application web
- Implémenter la validation d’entrée
- Inscription des itinéraires pour l’application web
- Implémentation de la gestion des erreurs et journalisation des erreurs

## <a name="overview"></a>Vue d'ensemble

Si vous débutez avec ASP.NET Web Forms, mais êtes familiarisé avec les concepts de programmation, vous avez le droit didacticiel. Si vous êtes déjà familiarisé avec ASP.NET Web Forms, vous pouvez bénéficier de cette série de didacticiels par les nouvelles fonctionnalités disponibles dans ASP.NET 4.5. Si vous n’êtes pas familiarisé avec les concepts de programmation et les Web Forms ASP.NET, consultez les didacticiels supplémentaires fournies dans les formulaires Web [mise en route](../../../index.md) section sur le site Web ASP.NET.

Spécifique au **dernière** ASP.NET 4.5 fonctionnalités fournies dans les formulaires Web cette série de didacticiels inclure les éléments suivants :

- Une interface utilisateur simple pour la création de projets à cette offre [prennent en charge plusieurs infrastructures d’ASP.NET](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add) (Web Forms, MVC et API Web).
- [Programme d’amorçage](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap), une infrastructure de mise en page et des thèmes qui fournit des fonctionnalités de conception et des thèmes réactives.
- [ASP.NET Identity](../../../../identity/index.md), un nouveau système d’appartenance ASP.NET qui fonctionne de la même dans toutes les infrastructures d’ASP.NET et fonctionne avec le logiciel autres qu’IIS d’hébergement web.
- [Entity Framework 6](https://msdn.microsoft.com/data/ef.aspx), une mise à jour à Entity Framework qui vous permet de récupérez et manipuler des données de manière fortement typée d’objets, accéder aux données de façon asynchrone, gérer les défaillances temporaires de connexion et consigner les instructions SQL.

Pour obtenir une liste complète des fonctionnalités ASP.NET 4.5, consultez [ASP.NET et Web Tools pour Visual Studio 2013 Release Notes](../../../../visual-studio/overview/2013/release-notes.md).

### <a name="the-wingtip-toys-sample-application"></a>L’exemple d’Application Wingtip Toys

Les captures d’écran suivantes fournissent un aperçu rapide de l’application ASP.NET Web forms que vous allez créer dans cette série de didacticiels. Lorsque vous exécutez l’application à partir de Visual Studio Express 2013 pour le Web, vous verrez la page d’accueil web suivantes.

![Wingtip Toys - page par défaut](introduction-and-overview/_static/image1.png)

Vous pouvez inscrire comme nouvel utilisateur, ou en tant qu’un utilisateur existant. Navigation est fournie en haut de chaque catégorie de produits en récupérant les produits disponibles à partir de la base de données.

En sélectionnant le lien de produits, vous serez en mesure d’afficher une liste de tous les produits disponibles.

![Wingtip Toys - produits](introduction-and-overview/_static/image2.png)

Vous pouvez également voir les détails des produits individuels en sélectionnant un des produits répertoriés.

![Wingtip Toys - détails du produit](introduction-and-overview/_static/image3.png)

En tant qu’utilisateur, vous pouvez inscrire et se connecter à l’aide de la fonctionnalité par défaut du modèle Web Forms. Ce didacticiel explique également comment se connecter à l’aide d’un compte Gmail existant. En outre, vous pouvez vous connecter en tant que l’administrateur pour ajouter et supprimer des produits à partir de la base de données.

![Wingtip Toys - connectez-vous](introduction-and-overview/_static/image4.png)

Une fois que vous êtes connecté en tant qu’utilisateur, vous pouvez ajouter des produits au panier d’achat et extraction avec PayPal. Notez que cet exemple d’application est conçue pour fonctionner avec un sandbox de développement de PayPal. Aucune transaction money réelle n’a lieu.

![Wingtip Toys - panier d’achat](introduction-and-overview/_static/image5.png)

PayPal confirmera votre compte, l’ordre et les informations de paiement.

![Wingtip Toys - PayPal](introduction-and-overview/_static/image6.png)

Après le retour à partir de PayPal, vous pouvez consulter et finaliser votre commande.

![Wingtip Toys - vérification de la commande](introduction-and-overview/_static/image7.png)

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous d’avoir les logiciels suivants installés sur votre ordinateur :

- [Microsoft Visual Studio 2013](https://www.microsoft.com/visualstudio/11/downloads#vs) ou [Microsoft Visual Studio Express 2013 pour le Web](https://www.microsoft.com/visualstudio/11/downloads#express-web). Le .NET Framework est installé automatiquement.

Cette série de didacticiels utilise Microsoft Visual Studio Express 2013 pour le Web. Pour terminer cette série de didacticiels, vous pouvez utiliser Microsoft Visual Studio Express 2013 pour le Web ou Microsoft Visual Studio 2013.

> [!NOTE] 
> 
> Microsoft Visual Studio 2013 et Microsoft Visual Studio Express 2013 pour le Web seront souvent être appelées en tant que Visual Studio tout au long de cette série de didacticiels.


Si vous avez déjà installée une version de Visual Studio, le processus d’installation installe Visual Studio 2013 ou Microsoft Visual Studio Express 2013 pour le Web en regard de la version existante. Les sites que vous avez créé dans les versions antérieures peuvent être ouverts dans Visual Studio 2013 et continuent à ouvrir dans les versions précédentes.

> [!NOTE] 
> 
> Cette procédure pas à pas suppose que vous avez sélectionné le *développement Web* collection de paramètres de la première fois que vous avez démarré Visual Studio. Pour plus d’informations, consultez [Comment : sélectionner des paramètres d’environnement de développement Web](https://msdn.microsoft.com/library/ff521558.aspx).


## <a name="download-the-sample-application"></a>Télécharger l’exemple d’Application

Après avoir installé les composants requis, vous êtes prêt à commencer à créer le projet Web est présenté dans cette série de didacticiels. Si vous souhaitez **éventuellement** exécuter l’exemple d’application qui crée de cette série de didacticiels, vous pouvez le télécharger depuis le site MSDN Samples. Ce téléchargement contient les éléments suivants :

- L’exemple d’application dans le *WingtipToys* dossier.
- Les ressources utilisées pour créer l’exemple d’application dans le *WingtipToys-composants* dossier dans le *WingtipToys* dossier.

#### <a name="download-the-file-from-msdn-samples-site"></a>Téléchargez le fichier à partir du site d’exemples MSDN :

[Prise en main 4.5 Web Forms ASP.NET et Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (c#) 

Le téléchargement est un *.zip* fichier. Pour afficher le projet terminé cette série de didacticiels crée, recherchez et sélectionnez le *c#*dossier dans le *.zip* fichier. Enregistrer le *c#* dossier dans le dossier que vous utilisez pour travailler avec les projets Visual Studio 2013. Par défaut, le dossier de projets Visual Studio 2013 est le suivant :

**C:\Users\*****&lt;nom d’utilisateur&gt;*** \Documents\Visual Studio 2013\Projects**

Renommer le ***c#*** dossier ***WingtipToys***.

> [!NOTE]
> Si vous avez déjà un dossier nommé *WingtipToys* dans votre dossier de projets, renommez temporairement ce dossier existant avant de renommer le *c#* dossier *WingtipToys*.


Pour exécuter le projet terminé, ouvrez le *WingtipToys* et double-cliquez sur le *WingtipToys.sln* fichier. Visual Studio 2013 s’ouvre le projet. Ensuite, cliquez sur le *Default.aspx* dans la fenêtre de l’Explorateur de solutions, cliquez sur Afficher dans le navigateur dans le menu contextuel.

### <a name="tutorial-support-and-comments"></a>Commentaires et prise en charge de didacticiel

Utilisez la section Q et r incluse avec le [prise en main de Web Forms ASP.NET 4.5 et Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) exemple (c#) pour toute question ou tout commentaire.

Commentaires sur cette série de didacticiels sont les bienvenus et lorsque la mise à jour de cette série de didacticiels tous les efforts sont effectués pour tenir des corrections de compte ou des suggestions d’amélioration est fournies dans les commentaires du didacticiels.

Lorsqu’une erreur se produit pendant le développement, ou si le site Web ne fonctionne pas correctement, les messages d’erreur peuvent donner des indices complexes à la source du problème ou ne peuvent pas expliquer comment la corriger. Pour résoudre certains problèmes courants, vous pouvez également utiliser le [forums ASP.NET](https://forums.asp.net/) ou la section Q et r inclus avec le [prise en main de Web Forms ASP.NET 4.5 et Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) () C#) exemple. Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez les didacticiels, veillez à consulter les emplacements ci-dessus.

>[!div class="step-by-step"]
[Next](create-the-project.md)
