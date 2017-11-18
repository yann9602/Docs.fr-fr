<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a><span data-ttu-id="17309-101">Générer automatiquement le modèle Movie</span><span class="sxs-lookup"><span data-stu-id="17309-101">Scaffold the Movie model</span></span>

* <span data-ttu-id="17309-102">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="17309-102">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="17309-103">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="17309-103">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```