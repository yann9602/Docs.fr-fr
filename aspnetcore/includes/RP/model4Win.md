<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a><span data-ttu-id="4b12a-101">Générer automatiquement le modèle Movie</span><span class="sxs-lookup"><span data-stu-id="4b12a-101">Scaffold the Movie model</span></span>

* <span data-ttu-id="4b12a-102">Ouvrez une fenêtre de commande dans le répertoire du projet (celui qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="4b12a-102">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4b12a-103">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="4b12a-103">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

<span data-ttu-id="4b12a-104">Si vous obtenez cette erreur :</span><span class="sxs-lookup"><span data-stu-id="4b12a-104">If you get the error:</span></span>
  ```
  The process cannot access the file 
 'RazorPagesMovie/bin/Debug/netcoreapp2.0/RazorPagesMovie.dll' 
  because it is being used by another process.
  ```

<span data-ttu-id="4b12a-105">Quittez Visual Studio et réexécutez la commande.</span><span class="sxs-lookup"><span data-stu-id="4b12a-105">Exit Visual Studio and run the command again.</span></span>