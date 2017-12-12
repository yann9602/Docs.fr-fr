---
uid: mvc/overview/older-versions/getting-started-with-aspnet-mvc3/vb/adding-a-model
title: "Ajout d’un modèle (VB) | Documents Microsoft"
author: Rick-Anderson
description: "Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application Web ASP.NET MVC à l’aide de Microsoft Visual Web Developer 2010 Express Service Pack 1, qui est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/12/2011
ms.topic: article
ms.assetid: b3aa7720-5c78-4ca2-baef-9a52234fb7ce
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-aspnet-mvc3/vb/adding-a-model
msc.type: authoredcontent
ms.openlocfilehash: efc18dd71e29d12dacc6cf84a1d3c7f7e92f520d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-a-model-vb"></a>Ajout d’un modèle (VB)
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application Web ASP.NET MVC à l’aide de Microsoft Visual Web Developer 2010 Express Service Pack 1, qui est une version gratuite de Microsoft Visual Studio. Avant de commencer, assurez-vous que vous avez installé les composants requis répertoriés ci-dessous. Vous pouvez installer tous les en cliquant sur le lien suivant : [Web Platform Installer](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack). Vous pouvez également installer individuellement les conditions préalables à l’aide des liens suivants :
> 
> - [Conditions préalables requises de Visual Studio Web Developer Express SP1](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)
> - [Mettre à jour des outils ASP.NET MVC 3](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=MVC3)
> - [SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)(runtime + outils prennent en charge)
> 
> Si vous utilisez Visual Studio 2010 au lieu de Visual Web Developer 2010, installez les composants requis en cliquant sur le lien suivant : [composants requis de Visual Studio 2010](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack).
> 
> Un projet de Visual Web Developer avec code VB.NET source est disponible pour accompagner cette rubrique. [Télécharger la version VB.NET](https://code.msdn.microsoft.com/Introduction-to-MVC-3-10d1b098). Si vous préférez c#, basculez vers le [c# version](../cs/adding-a-model.md) de ce didacticiel.


## <a name="adding-a-model"></a>Ajout d’un modèle

Dans cette section, vous allez ajouter des classes pour la gestion des films dans une base de données. Ces classes seront la partie « modèle » de l’application ASP.NET MVC.

Vous utiliserez une technologie d’accès aux données .NET Framework appelée Entity Framework pour définir et utiliser ces classes de modèle. Le prend en charge Entity Framework (souvent appelés EF) un paradigme de développement appelé *Code First*. Code permet tout d’abord vous permet de créer des objets de modèle en écrivant des classes simples. (Ceux-ci sont également appelés classes POCO, à partir de « plain-old CLR les objets. ») Vous pouvez ensuite configurer la base de données créé à la volée à partir de vos classes, ce qui permet un flux de travail de développement très propre et plus rapide.

## <a name="adding-model-classes"></a>Ajouter des Classes de modèle

Dans **l’Explorateur de solutions**, bouton droit sur le *modèles* dossier, sélectionnez **ajouter**, puis sélectionnez **classe**.

![](adding-a-model/_static/image1.png)

Nom de la classe « Film ».

Ajoutez les propriétés suivantes de cinq à la `Movie` classe :

[!code-vb[Main](adding-a-model/samples/sample1.vb)]

Nous allons utiliser la `Movie` classe pour représenter des films dans une base de données. Chaque instance d’un `Movie` objet correspond à une ligne dans une table de base de données et chaque propriété de la `Movie` classe est mappé à une colonne dans la table.

Dans le même fichier, ajoutez le code suivant `MovieDBContext` classe :

[!code-vb[Main](adding-a-model/samples/sample2.vb)]

Le `MovieDBContext` classe représente le contexte de base de données de film Entity Framework, qui gère l’extraction, de stockage et de mise à jour `Movie` instances dans une base de données de la classe. Le `MovieDBContext` dérive le `DbContext` fourni par Entity Framework de classe de base. Pour plus d’informations sur `DbContext` et `DbSet`, consultez [améliorations de productivité pour Entity Framework](https://blogs.msdn.com/b/efdesign/archive/2010/06/21/productivity-improvements-for-the-entity-framework.aspx?wa=wsignin1.0).

Afin de pouvoir faire référence à `DbContext` et `DbSet`, vous devez ajouter celle-ci `imports` instruction en haut du fichier :

[!code-vb[Main](adding-a-model/samples/sample3.vb)]

Le texte complet *Movie.vb* fichier est présenté ci-dessous.

[!code-vb[Main](adding-a-model/samples/sample4.vb)]

## <a name="creating-a-connection-string-and-working-with-sql-server-compact"></a>Création d’une chaîne de connexion et l’utilisation de SQL Server Compact

Le `MovieDBContext` classe créée gère la tâche de connexion à la base de données et de mappage `Movie` objets aux enregistrements de base de données. Une question que vous vous demandez peut-être, cependant, est comment spécifier une base de données auquel il se connecte à. Vous effectuerez qui en ajoutant des informations de connexion dans le *Web.config* fichier de l’application.

Ouvrez la racine de l’application *Web.config* fichier. (Pas le *Web.config* de fichiers dans le *vues* dossier.) L’image ci-dessous montrent deux *Web.config* fichiers ; ouvrir les *Web.config* fichier entourée en rouge.

![](adding-a-model/_static/image2.png)

## 

Ajouter la chaîne de connexion à la `<connectionStrings>` élément dans le *Web.config* fichier.

[!code-xml[Main](adding-a-model/samples/sample5.xml)]

L’exemple suivant montre une partie de la *Web.config* fichier avec la nouvelle chaîne de connexion ajoutée :

[!code-xml[Main](adding-a-model/samples/sample6.xml)]

Cette petite quantité de code et le XML est tout ce que vous devez écrire afin de représenter et de stocker les données de film dans une base de données.

Ensuite, vous allez générer un nouveau `MoviesController` classe que vous pouvez utiliser pour afficher les données de film et permettre aux utilisateurs de créer de nouvelles listes de film.

>[!div class="step-by-step"]
[Précédent](adding-a-view.md)
[Suivant](accessing-your-models-data-from-a-controller.md)
