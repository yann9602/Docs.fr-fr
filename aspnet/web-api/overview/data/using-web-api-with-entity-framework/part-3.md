---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-3
title: "Utilisez Migrations Code First pour amorcer la base de données | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: 76e2013a-65b7-488c-834d-9448ecea378e
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-3
msc.type: authoredcontent
ms.openlocfilehash: 1ca627397f0f100d13388f9afc27ff481886e098
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="use-code-first-migrations-to-seed-the-database"></a>Utilisez Migrations Code First pour amorcer la base de données
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](https://github.com/MikeWasson/BookService)

Dans cette section, vous allez utiliser [Migrations Code First](https://msdn.microsoft.com/data/jj591621) dans EF pour amorcer la base de données de test.

À partir de la **outils** menu, sélectionnez **Gestionnaire de Package de bibliothèque**, puis sélectionnez **Package Manager Console**. Dans la fenêtre de Console du Gestionnaire de Package, entrez la commande suivante :

[!code-console[Main](part-3/samples/sample1.cmd)]

Cette commande ajoute un dossier nommé Migrations à votre projet, ainsi qu’un fichier de code nommé Configuration.cs dans le dossier Migrations.

![](part-3/_static/image1.png)

Ouvrez le fichier Configuration.cs. Ajoutez le code suivant **à l’aide de** instruction.

[!code-csharp[Main](part-3/samples/sample2.cs)]

Puis ajoutez le code suivant à la **Configuration.Seed** méthode :

[!code-csharp[Main](part-3/samples/sample3.cs)]

Dans la fenêtre de Console du Gestionnaire de Package, tapez les commandes suivantes :

[!code-console[Main](part-3/samples/sample4.cmd)]

La première commande génère du code qui crée la base de données, et la deuxième commande exécute ce code. La base de données est créé localement, à l’aide de [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx).

![](part-3/_static/image2.png)

## <a name="explore-the-api-optional"></a>Explorer l’API (facultatif)

Appuyez sur F5 pour exécuter l’application en mode débogage. Visual Studio démarre IIS Express et s’exécute votre application web. Ensuite, Visual Studio lance un navigateur et ouvre la page d’accueil de l’application.

Lorsque Visual Studio est exécuté un projet web, elle affecte un numéro de port. Dans l’image ci-dessous, le numéro de port est 50524. Lorsque vous exécutez l’application, vous verrez un numéro de port différent.

![](part-3/_static/image3.png)

La page d’accueil est implémentée à l’aide d’ASP.NET MVC. En haut de la page, il existe un lien indiquant que « API ». Ce lien affiche une page d’aide générée automatiquement pour l’API web. (Pour savoir comment cette page d’aide est générée, et comment vous pouvez ajouter votre propre documentation à la page, consultez [création de Pages aide pour l’API Web ASP.NET](../../getting-started-with-aspnet-web-api/creating-api-help-pages.md).) Vous pouvez cliquer sur l’aide de liens de la page pour afficher les détails sur l’API, y compris le format de demande et de réponse.

![](part-3/_static/image4.png)

L’API permet les opérations CRUD sur la base de données. Voici un résumé de l’API.

| Auteurs |  |
| --- | -- |
| OBTENIR les api/auteurs | Obtenir tous les auteurs. |
| GET api/auteurs / {id} | Obtenir un auteur par ID. |
| Auteurs/api/POST | Créer un nouvel auteur. |
| PUT /api/authors/{id} | Mettre à jour un auteur existant. |
| DELETE /api/authors/{id} | Supprimer un auteur. |

| Documentation |  |
| --- | -- |
| OBTENIR /api/books | Obtenir tous les livres. |
| OBTENIR/API/books / {id} | Obtenir un livre par ID. |
| VALIDER la documentation/api / | Créer un nouveau. |
| PUT/API/books / {id} | Mettre à jour un livre existant. |
| Supprimer/API/books / {id} | Supprimer un livre. |

## <a name="view-the-database-optional"></a>Afficher la base de données (facultatif)

Lorsque vous avez exécuté la commande Update-Database, EF créé la base de données et appelé le `Seed` (méthode). Lorsque vous exécutez l’application localement, EF utilise [LocalDB](https://blogs.msdn.com/b/sqlexpress/archive/2011/07/12/introducing-localdb-a-better-sql-express.aspx). Vous pouvez afficher la base de données dans Visual Studio. À partir de la **vue** menu, sélectionnez **l’Explorateur d’objets SQL Server**.

![](part-3/_static/image5.png)

Dans le **se connecter au serveur** boîte de dialogue, dans le **nom du serveur** zone d’édition, tapez « (localdb) \v11.0 ». Laissez le **authentification** option « Authentification Windows ». Cliquez sur **Connexion**.

![](part-3/_static/image6.png)

Visual Studio se connecte à la base de données locale et affiche les bases de données existantes dans la fenêtre Explorateur d’objets SQL Server. Vous pouvez développer les nœuds pour afficher les tables EF créé.

![](part-3/_static/image7.png)

Pour afficher les données, cliquez sur une table, puis sélectionnez **des données d’affichage**.

![](part-3/_static/image8.png)

La capture d’écran suivante montre les résultats de la table de la documentation. Notez que EF rempli avec les données de valeur initiale, la base de données, et la table contient la clé étrangère à la table Authors.

![](part-3/_static/image9.png)

>[!div class="step-by-step"]
[Précédent](part-2.md)
[Suivant](part-4.md)
