---
uid: mvc/overview/getting-started/introduction/creating-a-connection-string
title: "Création d’une chaîne de connexion et l’utilisation de base de données SQL Server locale | Documents Microsoft"
author: Rick-Anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/17/2013
ms.topic: article
ms.assetid: 6127804d-c1a9-414d-8429-7f3dd0f56e97
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/introduction/creating-a-connection-string
msc.type: authoredcontent
ms.openlocfilehash: 41f1f30d86406580ab9fc7278a94d9c291913f9a
ms.sourcegitcommit: d1d8071d4093bf2444b5ae19d6e45c3d187e338b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2017
---
<a name="creating-a-connection-string-and-working-with-sql-server-localdb"></a>Création d’une chaîne de connexion et l’utilisation de base de données SQL Server locale
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

[!INCLUDE[Tutorial Note](sample/code-location.md)]

## <a name="creating-a-connection-string-and-working-with-sql-server-localdb"></a>Création d’une chaîne de connexion et l’utilisation de base de données SQL Server locale

Le `MovieDBContext` classe créée gère la tâche de connexion à la base de données et de mappage `Movie` objets aux enregistrements de base de données. Une question que vous vous demandez peut-être, cependant, est comment spécifier une base de données auquel il se connecte à. Vous n’avez pas réellement spécifier la base de données à utiliser, Entity Framework utilise par défaut [LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-2016-express-localdb). Dans cette section, nous ajouterons explicitement une chaîne de connexion dans le *Web.config* fichier de l’application.

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

[LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-2016-express-localdb) est une version allégée du moteur SQL Server Express de base de données qui démarre à la demande et s’exécute en mode utilisateur. Base de données locale s’exécute dans un mode spécial d’exécution de SQL Server Express qui vous permet de travailler avec les bases de données en tant que *.mdf* fichiers. En règle générale, les fichiers de base de données de base de données locale sont conservés dans le *application\_données* dossier d’un projet web.

SQL Server Express n’est pas recommandée pour une utilisation dans les applications web de production. LocalDB notamment ne doit pas être utilisé pour la production avec une application web, car il n’est pas conçu pour fonctionner avec les services IIS. Toutefois, une base de données de la base de données locale peut facilement être migré dans SQL Server ou SQL Azure.

Dans Visual Studio 2017, base de données locale est installé par défaut avec Visual Studio.

Par défaut, Entity Framework recherche une chaîne de connexion que le même nom que la classe de contexte d’objet (`MovieDBContext` pour ce projet). Pour plus d’informations, consultez [chaînes de connexion SQL Server pour les Applications Web ASP.NET](https://msdn.microsoft.com/en-us/library/jj653752.aspx).

Ouvrez la racine de l’application *Web.config* fichier ci-dessous. (Pas le *Web.config* de fichiers dans le *vues* dossier.)

![](creating-a-connection-string/_static/image1.png)

Rechercher les `<connectionStrings>` élément :

![](creating-a-connection-string/_static/image2.png)

Ajouter la chaîne de connexion à la `<connectionStrings>` élément dans le *Web.config* fichier.

[!code-xml[Main](creating-a-connection-string/samples/sample1.xml)]

L’exemple suivant montre une partie de la *Web.config* fichier avec la nouvelle chaîne de connexion ajoutée :

[!code-xml[Main](creating-a-connection-string/samples/sample2.xml)]

Les deux chaînes de connexion sont très similaires. La première chaîne de connexion est nommée `DefaultConnection` et est utilisé pour la base de données d’appartenance pour contrôler qui peut accéder à l’application. Vous avez ajouté la chaîne de connexion spécifie une base de données de la base de données locale nommée *Movie.mdf* situé dans le *application\_données* dossier. Nous ne sera pas utiliser la base de données d’appartenance dans ce didacticiel, pour plus d’informations sur l’appartenance, l’authentification et la sécurité, consultez mon didacticiel [créer une application ASP.NET MVC avec l’authentification et de la base de données SQL et le déployer vers Azure App Service](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data).

Le nom de la chaîne de connexion doit correspondre au nom de la [DbContext](https://msdn.microsoft.com/en-us/library/system.data.entity.dbcontext(v=vs.103).aspx) classe.

[!code-csharp[Main](creating-a-connection-string/samples/sample3.cs?highlight=15)]

Vous n’avez pas réellement besoin ajouter le `MovieDBContext` chaîne de connexion. Si vous ne spécifiez pas une chaîne de connexion, Entity Framework crée une base de données de la base de données locale dans le répertoire des utilisateurs avec le nom qualifié complet de le [DbContext](https://msdn.microsoft.com/en-us/library/system.data.entity.dbcontext(v=vs.103).aspx) classe (dans ce cas `MvcMovie.Models.MovieDBContext`). Vous pouvez nommer la base de données comme vous le souhaitez, tant qu’il a le *. MDF* suffixe. Par exemple, nous avons nom de la base de données *MyFilms.mdf*.

Ensuite, vous allez générer un nouveau `MoviesController` classe que vous pouvez utiliser pour afficher les données de film et permettre aux utilisateurs de créer de nouvelles listes de film.

>[!div class="step-by-step"]
[Précédent](adding-a-model.md)
[Suivant](accessing-your-models-data-from-a-controller.md)
