## <a name="add-initial-migration-and-update-the-database"></a>Ajouter la migration initiale et de mettre à jour de la base de données

* Ouvrez une invite de commandes et accédez au répertoire du projet. (Le répertoire contenant le *Startup.cs* fichier).

* À l’invite de commandes, exécutez les commandes suivantes :

  ```console
  dotnet restore
  dotnet ef migrations add Initial
  dotnet ef database update
  ```
  
  [.NET core](http://go.microsoft.com/fwlink/?LinkID=517853) est une implémentation multiplateforme de .NET. Voici comment ces commandes :

  * `dotnet restore`: Télécharge les packages NuGet spécifiés dans le *.csproj* fichier.
  * `dotnet ef migrations add Initial`Exécute la commande de migrations Entity Framework .NET Core CLI et crée la migration initiale. Le paramètre après « ajouter » est un nom que vous attribuez à la migration. Ici vous nommez la migration « Initial », car il s’agit de la migration de base de données initiale. Cette opération crée le */Migrations de données/\<date-heure > _Initial.cs* fichier contenant les commandes de migration pour ajouter le *film* table à la base de données.
  * `dotnet ef database update`Met à jour la base de données avec la migration, que nous avons créé.

Vous allez en savoir plus sur la chaîne de connexion et de la base de données dans le didacticiel suivant. Vous allez en savoir plus sur les modifications du modèle de données dans le [ajouter un champ](xref:tutorials/first-mvc-app/new-field) didacticiel.