---
title: "Utilisation de SQL Server LocalDB et d’ASP.NET Core"
author: rick-anderson
description: "Explique l’utilisation de SQL Server LocalDB et d’ASP.NET Core."
keywords: ASP.NET Core, Pages Razor, Razor, MVC, SQL, LocalDB
ms.author: riande
manager: wpickett
ms.date: 8/7/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 173bdcca80a599ec2d87ff4158614727b35f984a
ms.sourcegitcommit: d02d90b6272372178723ff932e8a9b9566afedb8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2017
---
# <a name="working-with-sql-server-localdb-and-aspnet-core"></a>Utilisation de SQL Server LocalDB et d’ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Joe Audette](https://twitter.com/joeaudette) 

L’objet `MovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données. Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` du fichier *Startup.cs* :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

Le système de [configuration](xref:fundamentals/configuration) d’ASP.NET Core lit `ConnectionString`. Pour un développement local, il obtient la chaîne de connexion à partir du fichier *appsettings.json* :

[!code-javascript[Main](razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=2&range=8-10)]

Quand vous déployez l’application sur un serveur de test ou de production, vous pouvez utiliser une variable d’environnement ou une autre approche pour définir un serveur SQL Server réel comme chaîne de connexion. Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration).

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB est une version allégée du moteur de base de données SQL Server Express qui est ciblé pour le développement de programmes. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, la base de données LocalDB crée des fichiers « \*.mdf» dans le répertoire *C:/Users/\<utilisateur\>*.

<a name="ssox"></a>
* Dans le menu **Affichage**, ouvrez **l’Explorateur d’objets SQL Server** (SSOX).

  ![Menu Affichage](sql/_static/ssox.png)

* Cliquez avec le bouton droit sur la table `Movie` **> Concepteur de vue**

  ![Menu contextuel ouvert sur la table Movie](sql/_static/design.png)

  ![Table Movie ouverte dans le Concepteur](sql/_static/dv.png)

Notez l’icône de clé en regard de `ID`. Par défaut, EF fait d’une propriété nommée `ID` la clé primaire.

* Cliquez avec le bouton droit sur la table `Movie` **> Afficher les données**

  ![Table Movie ouverte, affichant des données de table](sql/_static/vd22.png)

## <a name="seed-the-database"></a>Amorçage de la base de données

Créez une classe nommée `SeedData` dans l’espace de noms *Modèles*. Remplacez le code généré par ce qui suit :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/SeedData.cs?name=snippet_1)]

Si la base de données contient des films, l’initialiseur de valeur initiale retourne une valeur et aucun film n’est ajouté.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```
<a name="si"></a>
### <a name="add-the-seed-initializer"></a>Ajouter l’initialiseur de valeur initiale

Ajoutez l’initialiseur de valeur initiale à la fin de la méthode `Main` dans le fichier *Program.cs* :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Program.cs?highlight=6,17-32)]

Tester l’application

* Supprimez tous les enregistrements de la base de données. Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Forcez l’application à s’initialiser (appelez les méthodes de la classe `Startup`) pour que la méthode seed s’exécute. Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré. Pour ce faire, utilisez l’une des approches suivantes :

  * Cliquez avec le bouton droit sur l’icône de barre d’état système IIS Express dans la zone de notification, puis appuyez sur **Quitter** ou sur **Arrêter le site**

    ![Icône de la barre d’état système IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextuel](sql/_static/stopIIS.png)

   * Si vous exécutiez Visual Studio en mode de non-débogage, appuyez sur F5 pour l’exécuter en mode de débogage.
   * Si vous exécutiez Visual Studio en mode de débogage, arrêtez le débogueur et appuyez sur F5.
   
L’application affiche les données de valeurs de départ.

![Application Movie ouverte dans Chrome, affichant les données relatives aux films](sql/_static/m55.png)

Le didacticiel suivant nettoie la présentation des données.

>[!div class="step-by-step"]
[Précédent : Pages Razor obtenues par génération de modèles automatique](xref:tutorials/razor-pages/page)   
[Suivant : Mise à jour des pages](xref:tutorials/razor-pages/da1)
