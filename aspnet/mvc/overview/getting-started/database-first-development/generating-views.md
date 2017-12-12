---
uid: mvc/overview/getting-started/database-first-development/generating-views
title: "Base de données EF tout d’abord avec ASP.NET MVC : génération de vues | Documents Microsoft"
author: tfitzmac
description: "À l’aide de la structure d’ASP.NET MVC et Entity Framework, vous pouvez créer une application web qui fournit une interface à une base de données existante. Ce didacticiel seri..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/29/2014
ms.topic: article
ms.assetid: 669367cf-8e30-4eb6-821d-10a7d9bb906c
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/database-first-development/generating-views
msc.type: authoredcontent
ms.openlocfilehash: 5fccb3c56af0945ec448becff777a3e92dc160d7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="ef-database-first-with-aspnet-mvc-generating-views"></a>Base de données EF tout d’abord avec ASP.NET MVC : génération de vues
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> À l’aide de la structure d’ASP.NET MVC et Entity Framework, vous pouvez créer une application web qui fournit une interface à une base de données existante. Cette série de didacticiels vous montre comment automatiquement générer du code qui permet aux utilisateurs d’afficher, modifier, créer et supprimer des données qui résident dans une table de base de données. Le code généré correspond aux colonnes dans la table de base de données.
> 
> Cette partie de la série consacrée à l’aide de la génération de modèles automatique ASP.NET pour générer les contrôleurs et les vues.


## <a name="add-scaffold"></a>Ajouter une vue de structure

Vous êtes prêt à générer le code qui fournira les opérations de données standard pour les classes du modèle. Vous ajoutez le code en ajoutant un élément de la vue de structure. Il existe de nombreuses options pour le type de la structure que vous pouvez ajouter ; Dans ce didacticiel, la vue inclura un contrôleur et les vues qui correspondent aux modèles Student et d’inscription, que vous avez créé dans la section précédente.

Pour maintenir la cohérence dans votre projet, vous allez ajouter le nouveau contrôleur à l’objet existant **contrôleurs** dossier. Avec le bouton droit le **contrôleurs** dossier, puis sélectionnez **ajouter** – **un nouvel élément structuré**.

![Ajouter une vue de structure](generating-views/_static/image1.png)

Sélectionnez le **contrôleur MVC 5 avec vues, utilisant Entity Framework** option. Cette option génère le contrôleur et les vues pour la mise à jour, suppression, création et affichage des données dans votre modèle.

![ajouter le contrôleur mvc](generating-views/_static/image2.png)

Sélectionnez **Student** pour la classe de modèle, puis sélectionnez le **ContosoUniversityEntities** pour la classe de contexte. Conservez le nom de contrôleur en tant que **StudentsController**,

![Spécifiez le contrôleur](generating-views/_static/image3.png)

Cliquez sur **Ajouter**.

Si vous recevez une erreur, il peut être, car vous n’avez pas généré le projet dans la section précédente. Dans ce cas, essayez de générer le projet, puis ajoutez de nouveau l’élément généré automatiquement.

Une fois le processus de génération de code est terminé, vous verrez un nouveau contrôleur et les vues dans votre projet.

![afficher les affichages](generating-views/_static/image4.png)

Effectuer les mêmes étapes à nouveau, mais ajouter une structure de la classe de l’inscription. Lorsque vous avez terminé, vous devez avoir un **EnrollmentsController.cs** fichier et un dossier sous **vues** nommé **inscriptions** avec Create, Delete, détails, modifier et Index Affichage.

![afficher les affichages](generating-views/_static/image5.png)

## <a name="add-links-to-new-views"></a>Ajouter des liens vers les nouveaux affichages

Pour le rendre plus facile pour vous permettent d’accéder à vos vues de nouveau, vous pouvez ajouter deux liens hypertexte aux vues des Index pour les étudiants et les inscriptions. Ouvrez le fichier **Views/Home/Index.cshtml**, qui est la page d’accueil de votre site. Ajoutez le code suivant sous le jumbotron.

[!code-cshtml[Main](generating-views/samples/sample1.cshtml)]

Pour la méthode ActionLink, le premier paramètre est le texte à afficher dans le lien. Le deuxième paramètre est l’action et le troisième paramètre est le nom du contrôleur. Par exemple, le premier lien pointe vers l’action de l’Index dans StudentsController. Le lien hypertexte est construit à partir de ces valeurs. Le premier lien finalement mène à la **Index.cshtml** fichier inclus dans le **vues/étudiants** dossier.

## <a name="display-student-views"></a>Afficher des vues d’étudiant

Vous allez vérifier que le code ajouté à votre projet correctement affiche une liste des étudiants et permet aux utilisateurs de modifier, créer ou supprimer les enregistrements de l’étudiant dans la base de données.

Cliquez sur le **Views/Home/Index.cshtml** de fichiers, puis sélectionnez **afficher dans le navigateur**. Dans cette page, cliquez sur le lien pour obtenir la liste d’étudiants.

![](generating-views/_static/image6.png)

Dans cette page, notez la liste des étudiants et des liens pour modifier ces données.

![liste d’étudiants](generating-views/_static/image7.png)

Cliquez sur le **créer un nouveau** lier et fournir des valeurs pour un nouvel étudiant.

![Créer nouvel étudiant](generating-views/_static/image8.png)

Cliquez sur **créer**et notez le nouvel étudiant est ajouté à votre liste.

![liste avec le nouvel étudiant](generating-views/_static/image9.png)

Sélectionnez le **modifier** la liaison et modifier certains des valeurs pour un étudiant.

![modifier les étudiants](generating-views/_static/image10.png)

Cliquez sur **enregistrer**et notez l’enregistrement de l’étudiant a été modifié.

Enfin, sélectionnez le **supprimer** lier et confirmer que vous souhaitez supprimer l’enregistrement en cliquant sur le **supprimer** bouton.

![supprimer les étudiants](generating-views/_static/image11.png)

Sans écrire de code, vous avez ajouté des vues qui effectuent des opérations courantes sur les données dans la table d’étudiants.

Vous avez peut-être remarqué que l’étiquette de texte pour un champ est basé sur la propriété de base de données (tel que **LastName**) qui n’est pas nécessairement ce que vous souhaitez afficher sur la page web. Par exemple, vous souhaiterez peut-être l’étiquette **nom**. Résoudre ce problème d’affichage plus loin dans le didacticiel.

## <a name="display-enrollment-views"></a>Afficher des vues de l’inscription

Votre base de données inclut une relation un-à-plusieurs entre les tables Student et d’inscription et d’une relation un-à-plusieurs entre les tables en cours et d’inscription. Les vues pour l’inscription de gérer correctement ces relations. Accédez à la page d’accueil de votre site, puis sélectionnez le **liste des inscriptions** lien, puis le **créer un nouveau** lien. La vue affiche le formulaire de création d’un nouvel enregistrement d’inscription. En particulier, notez que le formulaire contient deux listes déroulantes sont remplies avec des valeurs de tables connexes.

![créer l’inscription](generating-views/_static/image12.png)

En outre, la validation de valeurs fournies est appliquée automatiquement en fonction du type de données du champ. Niveau requiert un nombre, afin qu’un message d’erreur est affiché si vous essayez de fournir une valeur incompatible.

![message de validation](generating-views/_static/image13.png)

Vous avez vérifié que les vues générées automatiquement permettent aux utilisateurs de travailler avec les données dans la base de données. Dans l’étape suivante du didacticiel de cette série, vous mettez à jour la base de données et apporte les modifications correspondantes dans l’application web.

>[!div class="step-by-step"]
[Précédent](creating-the-web-application.md)
[Suivant](changing-the-database.md)
