<a name="cs"></a>
### <a name="add-a-database-connection-string"></a><span data-ttu-id="ce9d8-101">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="ce9d8-101">Add a database connection string</span></span>

<span data-ttu-id="ce9d8-102">Ajoutez une chaîne de connexion au fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ce9d8-102">Add a connection string to the *appsettings.json* file.</span></span>

[!code-json[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

<a name="reg"></a>
###  <a name="register-the-database-context"></a><span data-ttu-id="ce9d8-103">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="ce9d8-103">Register the database context</span></span>

<span data-ttu-id="ce9d8-104">Inscrivez le contexte de base de données auprès du conteneur [d’injection de dépendance](xref:fundamentals/dependency-injection) dans le fichier *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ce9d8-104">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in the *Startup.cs* file.</span></span>