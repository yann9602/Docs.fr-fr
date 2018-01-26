<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a>Effectuer la génération de modèles automatique pour le modèle Movie

* Exécutez la commande suivante dans une ligne de commande (dans le répertoire du projet qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*) :

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

Si vous obtenez l’erreur :
  ```
No executable found matching command "dotnet-aspnet-codegenerator"
  ```

Ouvrez une interface de commande dans le répertoire du projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).
