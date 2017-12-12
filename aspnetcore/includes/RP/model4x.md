<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a>Générer automatiquement le modèle Movie

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).
* Exécutez la commande suivante :

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```
  
Si vous obtenez l’erreur :
  ```
No executable found matching command "dotnet-aspnet-codegenerator"
  ```

Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).