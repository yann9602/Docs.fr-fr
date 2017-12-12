---
uid: mvc/overview/older-versions/getting-started-with-aspnet-mvc4/adding-a-model
title: "Ajout d’un modèle | Documents Microsoft"
author: Rick-Anderson
description: "Remarque : Une version mise à jour de ce didacticiel est disponible ici qui utilise ASP.NET MVC 5 et Visual Studio 2013. Il est plus sécurisé, beaucoup plus simple à suivre et de démonstration..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/28/2012
ms.topic: article
ms.assetid: 53db72da-e0b9-44d9-b60b-6e6988c00b28
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-aspnet-mvc4/adding-a-model
msc.type: authoredcontent
ms.openlocfilehash: 1d066e4bab866a2195647f43aa886279fee941db
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-a-model"></a>Ajout d’un modèle
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> > [!NOTE]
> > Une version mise à jour de ce didacticiel est disponible [ici](../../getting-started/introduction/getting-started.md) qui utilise ASP.NET MVC 5 et Visual Studio 2013. Il est plus sécurisé, beaucoup plus simple à suivre et illustre plusieurs fonctionnalités.


Dans cette section, vous allez ajouter des classes pour la gestion des films dans une base de données. Ces classes seront les &quot;modèle&quot; dans le cadre de l’application ASP.NET MVC.

Vous allez utiliser une technologie d’accès aux données .NET Framework appelée le [Entity Framework](https://msdn.microsoft.com/en-us/library/bb399572(VS.110).aspx) pour définir et utiliser ces classes de modèle. Le prend en charge Entity Framework (souvent appelés EF) un paradigme de développement appelé *Code First*. Code permet tout d’abord vous permet de créer des objets de modèle en écrivant des classes simples. (Ils sont également appelés classes POCO, à partir de &quot;objets de brut-old CLR.&quot;) Vous pouvez ensuite configurer la base de données créé à la volée à partir de vos classes, ce qui permet un flux de travail de développement très propre et plus rapide.

## <a name="adding-model-classes"></a>Ajouter des Classes de modèle

Dans **l’Explorateur de solutions**, bouton droit sur le *modèles* dossier, sélectionnez **ajouter**, puis sélectionnez **classe**.

![](adding-a-model/_static/image1.png)

Entrez le *classe* nom &quot;film&quot;.

Ajoutez les propriétés suivantes de cinq à la `Movie` classe :

[!code-csharp[Main](adding-a-model/samples/sample1.cs)]

Nous allons utiliser la `Movie` classe pour représenter des films dans une base de données. Chaque instance d’un `Movie` objet correspond à une ligne dans une table de base de données et chaque propriété de la `Movie` classe est mappé à une colonne dans la table.

Dans le même fichier, ajoutez le code suivant `MovieDBContext` classe :

[!code-csharp[Main](adding-a-model/samples/sample2.cs)]

Le `MovieDBContext` classe représente le contexte de base de données de film Entity Framework, qui gère l’extraction, de stockage et de mise à jour `Movie` instances dans une base de données de la classe. Le `MovieDBContext` dérive le `DbContext` fourni par Entity Framework de classe de base.

Afin de pouvoir faire référence à `DbContext` et `DbSet`, vous devez ajouter celle-ci `using` instruction en haut du fichier :

[!code-csharp[Main](adding-a-model/samples/sample3.cs)]

Le texte complet *Movie.cs* fichier est présenté ci-dessous. (Plusieurs à l’aide des instructions qui ne sont pas nécessaires ont été supprimée.)

[!code-csharp[Main](adding-a-model/samples/sample4.cs)]

## <a name="creating-a-connection-string-and-working-with-sql-server-localdb"></a>Création d’une chaîne de connexion et l’utilisation de base de données SQL Server locale

Le `MovieDBContext` classe créée gère la tâche de connexion à la base de données et de mappage `Movie` objets aux enregistrements de base de données. Une question que vous vous demandez peut-être, cependant, est comment spécifier une base de données auquel il se connecte à. Vous effectuerez qui en ajoutant des informations de connexion dans le *Web.config* fichier de l’application.

Ouvrez la racine de l’application *Web.config* fichier. (Pas le *Web.config* de fichiers dans le *vues* dossier.) Ouvrez le *Web.config* fichier indiqué en rouge.

![](adding-a-model/_static/image2.png)

Ajouter la chaîne de connexion à la `<connectionStrings>` élément dans le *Web.config* fichier.

[!code-xml[Main](adding-a-model/samples/sample5.xml)]

L’exemple suivant montre une partie de la *Web.config* fichier avec la nouvelle chaîne de connexion ajoutée :

[!code-xml[Main](adding-a-model/samples/sample6.xml?highlight=6-9)]

Cette petite quantité de code et le XML est tout ce que vous devez écrire afin de représenter et de stocker les données de film dans une base de données.

Ensuite, vous allez générer un nouveau `MoviesController` classe que vous pouvez utiliser pour afficher les données de film et permettre aux utilisateurs de créer de nouvelles listes de film.

>[!div class="step-by-step"]
[Précédent](adding-a-view.md)
[Suivant](accessing-your-models-data-from-a-controller.md)
