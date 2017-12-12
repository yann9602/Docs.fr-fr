---
uid: mvc/overview/getting-started/introduction/adding-a-model
title: "Ajout d’un modèle | Documents Microsoft"
author: Rick-Anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/28/2015
ms.topic: article
ms.assetid: 276552b5-f349-4fcf-8f40-6d042f7aa88e
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-a-model
msc.type: authoredcontent
ms.openlocfilehash: 13aab58e86829a8d4accd1d304420dcb34ffa472
ms.sourcegitcommit: ec9371e2fbfcb8d62e7e7cae69e7752f3f205385
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
<a name="adding-a-model"></a>Ajout d’un modèle
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

[!INCLUDE[Tutorial Note](sample/code-location.md)]

Dans cette section, vous allez ajouter des classes pour la gestion des films dans une base de données. Ces classes seront les &quot;modèle&quot; dans le cadre de l’application ASP.NET MVC.

Vous allez utiliser une technologie d’accès aux données .NET Framework appelée le [Entity Framework](https://docs.microsoft.com/ef/) pour définir et utiliser ces classes de modèle. Le prend en charge Entity Framework (souvent appelés EF) un paradigme de développement appelé *Code First*. Code permet tout d’abord vous permet de créer des objets de modèle en écrivant des classes simples. (Ils sont également appelés classes POCO, à partir de &quot;objets de brut-old CLR.&quot;) Vous pouvez ensuite configurer la base de données créé à la volée à partir de vos classes, ce qui permet un flux de travail de développement très propre et plus rapide. Si vous devez d’abord créer la base de données, vous pouvez toujours suivre ce didacticiel pour en savoir plus sur le développement d’applications MVC et EF. Vous pouvez alors suivre Tom Fizmakens [génération de modèles automatique ASP.NET](xref:visual-studio/overview/2013/aspnet-scaffolding-overview) (didacticiel), qui couvre la première approche de base de données.

## <a name="adding-model-classes"></a>Ajouter des Classes de modèle

Dans **l’Explorateur de solutions**, bouton droit sur le *modèles* dossier, sélectionnez **ajouter**, puis sélectionnez **classe**.

![](adding-a-model/_static/image1.png)

Entrez le *classe* nom &quot;film&quot;.

Ajoutez les propriétés suivantes de cinq à la `Movie` classe :

[!code-csharp[Main](adding-a-model/samples/sample1.cs)]

Nous allons utiliser la `Movie` classe pour représenter des films dans une base de données. Chaque instance d’un `Movie` objet correspond à une ligne dans une table de base de données et chaque propriété de la `Movie` classe est mappé à une colonne dans la table.

Remarque : Pour pouvoir utiliser System.Data.Entity et la classe associée, vous devez installer le [Entity Framework NuGet Package](https://www.nuget.org/packages/EntityFramework/). Suivez le lien pour obtenir des instructions.

Dans le même fichier, ajoutez le code suivant `MovieDBContext` classe :

[!code-csharp[Main](adding-a-model/samples/sample2.cs?highlight=2,15-18)]

Le `MovieDBContext` classe représente le contexte de base de données de film Entity Framework, qui gère l’extraction, de stockage et de mise à jour `Movie` instances dans une base de données de la classe. Le `MovieDBContext` dérive le `DbContext` fourni par Entity Framework de classe de base.

Afin de pouvoir faire référence à `DbContext` et `DbSet`, vous devez ajouter celle-ci `using` instruction en haut du fichier :

[!code-csharp[Main](adding-a-model/samples/sample3.cs)]

Ce faire, vous pouvez ajouter manuellement à l’aide de l’instruction, ou vous pouvez survoler les traits rouges ondulés, cliquez sur `Show potential fixes` et cliquez sur`using System.Data.Entity;`

![](adding-a-model/_static/image2.png)

Remarque : Plusieurs inutilisé `using` instructions ont été supprimées. Visual Studio affiche les dépendances non utilisées en gris. Vous pouvez supprimer les dépendances d’unnused en pointant sur les dépendances en gris, cliquez sur `Show potential fixes` et cliquez sur **supprimer les instructions Using obsolètes.**

![](adding-a-model/_static/image3.png)

Nous avons ajouté enfin d’un modèle (le M dans MVC). Dans la section suivante, vous allez travailler avec la chaîne de connexion de base de données.

>[!div class="step-by-step"]
[Précédent](adding-a-view.md)
[Suivant](creating-a-connection-string.md)
