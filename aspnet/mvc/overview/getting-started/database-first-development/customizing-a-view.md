---
uid: mvc/overview/getting-started/database-first-development/customizing-a-view
title: "Base de données EF tout d’abord avec ASP.NET MVC : personnaliser un affichage | Documents Microsoft"
author: tfitzmac
description: "À l’aide de la structure d’ASP.NET MVC et Entity Framework, vous pouvez créer une application web qui fournit une interface à une base de données existante. Ce didacticiel seri..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/01/2014
ms.topic: article
ms.assetid: 269380ff-d7e1-4035-8ad1-fe1316a25f76
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/database-first-development/customizing-a-view
msc.type: authoredcontent
ms.openlocfilehash: af9609396cff18b08824732731ddb9c5cca578fa
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="ef-database-first-with-aspnet-mvc-customizing-a-view"></a>Base de données EF tout d’abord avec ASP.NET MVC : personnaliser un affichage
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> À l’aide de la structure d’ASP.NET MVC et Entity Framework, vous pouvez créer une application web qui fournit une interface à une base de données existante. Cette série de didacticiels vous montre comment automatiquement générer du code qui permet aux utilisateurs d’afficher, modifier, créer et supprimer des données qui résident dans une table de base de données. Le code généré correspond aux colonnes dans la table de base de données.
> 
> Cette partie de la série se concentre sur la modification des vues générés automatiquement pour améliorer la présentation.


## <a name="add-enrolled-courses-to-student-details"></a>Ajouter des cours inscrits pour les détails de l’étudiant

Le code généré fournit un bon point de départ pour votre application, mais il n’en fournit pas nécessairement toutes les fonctionnalités dont vous avez besoin dans votre application. Vous pouvez personnaliser le code pour répondre aux besoins particuliers de votre application. Actuellement, votre application n’affiche pas les cours inscrits pour l’étudiant sélectionné. Dans cette section, vous allez ajouter les cours inscrits pour chaque étudiant à la **détails** view pour les étudiants.

Ouvrez **Students/Details.cshtml**et en dessous de la dernière &lt;/dl&gt; onglet, mais avant la fermeture &lt;/div&gt; , ajoutez le code suivant.

[!code-cshtml[Main](customizing-a-view/samples/sample1.cshtml)]

Ce code crée une table qui affiche une ligne pour chaque enregistrement dans la table d’inscription pour les étudiants sélectionné. Le **affichage** méthode effectue le rendu HTML de l’objet qui représente l’expression (modelItem). Vous utilisez la méthode Display (au lieu d’incorporation simplement la valeur de propriété dans le code) pour vous assurer que la valeur est mise en forme correctement selon son type et le modèle pour ce type. Dans cet exemple, chaque expression retourne une propriété unique de l’enregistrement actif dans la boucle, et les valeurs sont des types primitifs qui sont rendus sous forme de texte.

Accédez de nouveau à la vue étudiants / d’Index et sélectionnez **détails** pour l’une des étudiants. Vous verrez que les cours inscrits ont été incluses dans la vue.

![étudiant avec l’inscription](customizing-a-view/_static/image1.png)

>[!div class="step-by-step"]
[Précédent](changing-the-database.md)
[Suivant](enhancing-data-validation.md)
