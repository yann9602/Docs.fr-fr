# <a name="adding-a-model-to-an-aspnet-core-mvc-app"></a>Ajout d’un modèle dans une application ASP.NET Core MVC

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/tdykstra)

Dans cette section, vous ajoutez des classes pour la gestion des films d’une base de données. Ces classes constituent la partie « **M**odèle » de l’application **M**VC.

Vous utilisez ces classes avec [Entity Framework Core](https://docs.microsoft.com/ef/core) (EF Core) pour travailler avec une base de données. EF Core est un framework de mappage relationnel d’objets qui simplifie le code d’accès aux données à écrire. [EF Core prend en charge de nombreux moteurs de base de données](https://docs.microsoft.com/ef/core/providers/).

Les classes de modèle que vous créez portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core. Elles définissent simplement les propriétés des données stockées dans la base de données.

Dans ce didacticiel, vous écrivez d’abord les classes du modèle, puis EF Core crée la base de données. Une autre approche que nous ne décrivons pas ici consiste à générer les classes du modèle à partir d’une base de données déjà existante. Pour plus d’informations sur cette approche, consultez [Getting Started with EF Core on ASP.NET Core with an Existing Database](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db).

## <a name="add-a-data-model-class"></a>Ajouter une classe de modèle de données
