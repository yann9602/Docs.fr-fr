---
uid: mvc/overview/getting-started/database-first-development/creating-the-web-application
title: "Base de données EF tout d’abord avec ASP.NET MVC : création de l’Application Web et les modèles de données | Documents Microsoft"
author: tfitzmac
description: "À l’aide de la structure d’ASP.NET MVC et Entity Framework, vous pouvez créer une application web qui fournit une interface à une base de données existante. Ce didacticiel seri..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/01/2014
ms.topic: article
ms.assetid: bc8f2bd5-ff57-4dcd-8418-a5bd517d8953
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/database-first-development/creating-the-web-application
msc.type: authoredcontent
ms.openlocfilehash: f495bfa3aa5332e4ca3e44c2ffbfb760fbbeafc8
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="ef-database-first-with-aspnet-mvc-creating-the-web-application-and-data-models"></a>Base de données EF tout d’abord avec ASP.NET MVC : création de l’Application Web et les modèles de données
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> À l’aide de la structure d’ASP.NET MVC et Entity Framework, vous pouvez créer une application web qui fournit une interface à une base de données existante. Cette série de didacticiels vous montre comment automatiquement générer du code qui permet aux utilisateurs d’afficher, modifier, créer et supprimer des données qui résident dans une table de base de données. Le code généré correspond aux colonnes dans la table de base de données.
> 
> Cette partie de la série se concentre sur la création de l’application web et générer les modèles de données en fonction de vos tables de base de données.


## <a name="create-a-new-aspnet-web-application"></a>Créez une Application Web ASP.NET

Dans une solution ou dans la même solution que le projet de base de données, créez un nouveau projet dans Visual Studio et sélectionnez le **Application Web ASP.NET** modèle. Nommez le projet **ContosoSite**.

![créer le projet](creating-the-web-application/_static/image1.png)

Cliquez sur **OK**.

Dans la fenêtre Nouveau projet ASP.NET, sélectionnez le **MVC** modèle. Vous pouvez effacer le **hôte dans le cloud** option pour l’instant, car vous allez déployer l’application vers le cloud plus tard. Cliquez sur **OK** pour créer l’application.

![Sélectionnez le modèle mvc](creating-the-web-application/_static/image2.png)

Le projet est créé avec les fichiers par défaut et les dossiers.

Dans ce didacticiel, vous allez utiliser Entity Framework 6. Vous pouvez vérifier la version d’Entity Framework dans votre projet via la fenêtre Gérer les Packages NuGet. Si nécessaire, mettez à jour votre version d’Entity Framework.

![afficher la version](creating-the-web-application/_static/image3.png)

## <a name="generate-the-models"></a>Générer les modèles

Vous allez maintenant créer les modèles Entity Framework à partir des tables de base de données. Ces modèles sont des classes que vous allez utiliser pour travailler avec les données. Chaque modèle reflète une table dans la base de données et contient des propriétés qui correspondent aux colonnes dans la table.

Avec le bouton droit le **modèles** dossier, puis sélectionnez **ajouter** et **un nouvel élément**.

![Ajouter un nouvel élément](creating-the-web-application/_static/image4.png)

Dans la fenêtre Ajouter un nouvel élément, sélectionnez **données** dans le volet gauche et **ADO.NET Entity Data Model** parmi les options dans le volet central. Nommez le nouveau fichier de modèle **ContosoModel**.

![créer le modèle](creating-the-web-application/_static/image5.png)

Cliquez sur **Ajouter**.

Dans l’Assistant Entity Data Model, sélectionnez **EF Designer à partir de la base de données**.

![générer à partir de la base de données](creating-the-web-application/_static/image6.png)

Cliquez sur **Suivant**.

Si vous avez des connexions de base de données définies dans votre environnement de développement, vous pouvez voir une de ces connexions présélectionnées. Toutefois, vous souhaitez créer une nouvelle connexion à la base de données que vous avez créé dans la première partie de ce didacticiel. Cliquez sur le **nouvelle connexion** bouton.

![se connecter à la base de données](creating-the-web-application/_static/image7.png)

Dans la fenêtre Propriétés de connexion, indiquez le nom du serveur local où votre base de données a été créé (dans ce cas **(localdb) \ProjectsV12**). Après avoir entré le nom du serveur, sélectionnez le ContosoUniversityData à partir de bases de données disponibles.

![définir les propriétés de connexion](creating-the-web-application/_static/image8.png)

Cliquez sur **OK**.

Les propriétés de connexion correctes sont maintenant affichées. Vous pouvez utiliser le nom par défaut pour la connexion dans le fichier Web.Config

![paramètres de connexion](creating-the-web-application/_static/image9.png)

Cliquez sur **Suivant**.

Sélectionnez **Tables** pour générer des modèles pour les trois tables.

![Sélectionner des tables](creating-the-web-application/_static/image10.png)

Cliquez sur **Terminer**.

Si vous recevez un avertissement de sécurité, sélectionnez **OK** pour continuer l’exécution du modèle.

Les modèles sont générés à partir de la base de données et un diagramme s’affiche et présente les propriétés et les relations entre les tables.

![diagramme du modèle](creating-the-web-application/_static/image11.png)

Le dossier de modèles inclut désormais les nombreux nouveaux fichiers liés aux modèles qui ont été générées à partir de la base de données.

![afficher les nouveaux fichiers de modèle](creating-the-web-application/_static/image12.png)

Le **ContosoModel.Context.cs** fichier contient une classe qui dérive de la **DbContext** classe et fournit une propriété pour chaque classe de modèle qui correspond à une table de base de données. Le **Course.cs**, **Enrollment.cs**, et **Student.cs** fichiers contiennent les classes qui représentent les tables de bases de données du modèle. Lorsque vous travaillez avec la structure, vous allez utiliser la classe de contexte et les classes du modèle.

Avant de poursuivre ce didacticiel, générez le projet. Dans la section suivante, vous allez générer du code basé sur les modèles de données, mais cette section ne fonctionne pas si le projet n’a pas été généré.

>[!div class="step-by-step"]
[Précédent](setting-up-database.md)
[Suivant](generating-views.md)
