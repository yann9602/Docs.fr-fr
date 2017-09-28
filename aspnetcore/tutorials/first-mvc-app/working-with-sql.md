---
title: Utilisation de SQL Server LocalDB
author: rick-anderson
description: Utilisation de SQL Server LocalDB avec une application MVC simple
keywords: ASP.NET Core, SQL Server LocalDB, SQL Server, LocalDB
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.assetid: ff8fd9b8-7c98-424d-8641-7524e23bf541
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: e44b6de13540d93337bf9a128d287808cffbfb46
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="working-with-sql-server-localdb"></a>Utilisation de SQL Server LocalDB

De [Rick Anderson](https://twitter.com/RickAndMSFT)

L’objet `MvcMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données. Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` du fichier *Startup.cs* :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]

Le système de [configuration](xref:fundamentals/configuration) d’ASP.NET Core lit `ConnectionString`. Pour un développement local, il obtient la chaîne de connexion à partir du fichier *appsettings.json* :

[!code-javascript[Main](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

Quand vous déployez l’application sur un serveur de test ou de production, vous pouvez utiliser une variable d’environnement ou une autre approche pour définir un serveur SQL Server réel comme chaîne de connexion. Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration).

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB est une version allégée du moteur de base de données SQL Server Express qui est ciblée pour le développement de programmes. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, la base de données LocalDB crée des fichiers « \*.mdf » dans le répertoire *C:/Users/\<utilisateur\>*.

* Dans le menu **Affichage**, ouvrez **l’Explorateur d’objets SQL Server** (SSOX).

  ![Menu View](working-with-sql/_static/ssox.png)

* Cliquez avec le bouton droit sur la table `Movie` **> Concepteur de vue**.

  ![Menu contextuel ouvert sur la table Movie](working-with-sql/_static/design.png)

  ![Table Movie ouverte dans le Concepteur](working-with-sql/_static/dv.png)

Notez l’icône de clé en regard de `ID`. Par défaut, EF fait d’une propriété nommée `ID` la clé primaire.

* Cliquez avec le bouton droit sur la table `Movie` **> Afficher les données**.

  ![Menu contextuel ouvert sur la table Movie](working-with-sql/_static/ssox2.png)

  ![Table Movie ouverte, affichant des données de table](working-with-sql/_static/vd22.png)

## <a name="seed-the-database"></a>Amorcer la base de données

Créez une classe nommée `SeedData` dans l’espace de noms *Modèles*. Remplacez le code généré par ce qui suit :

[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]

Si la base de données contient des films, l’initialiseur de valeur initiale retourne une valeur et aucun film n’est ajouté.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a>Ajouter l’initialiseur de valeur initiale

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Ajoutez l’initialiseur de valeur initiale à la méthode `Main` dans le fichier *Program.cs* :

[!code-csharp[Main](start-mvc/sample/MvcMovie/Program.cs?highlight=6,14-32)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Ajoutez l’initialiseur de valeur initiale à la fin de la méthode `Configure` dans le fichier *Startup.cs* :

[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=9&name=snippet_seed)]

---

Tester l’application

* Supprimez tous les enregistrements de la base de données. Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de SSOX.
* Forcez l’application à s’initialiser (appelez les méthodes de la classe `Startup`) pour que la méthode seed s’exécute. Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré. Pour cela, adoptez l’une des approches suivantes :

  * Cliquez avec le bouton droit sur l’icône de barre d’état système IIS Express dans la zone de notification, puis appuyez sur **Quitter** ou sur **Arrêter le site**.

    ![Icône de la barre d’état système IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu contextuel](working-with-sql/_static/stopIIS.png)

   * Si vous exécutiez Visual Studio en mode non-débogage, appuyez sur F5 pour l’exécuter en mode débogage.
   * Si vous exécutiez Visual Studio en mode débogage, arrêtez le débogueur et appuyez sur F5.
   
L’application affiche les données de départ.

![Application MVC Movie ouverte dans Microsoft Edge, affichant les données relatives aux films](working-with-sql/_static/m55.png)

>[!div class="step-by-step"]
[Précédent](adding-model.md)
[Suivant](controller-methods-views.md)  
