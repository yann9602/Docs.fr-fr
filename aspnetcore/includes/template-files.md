* Startup.cs : [classe Startup](../fundamentals/startup.md) -classe configure le pipeline de demande qui gère toutes les demandes effectuées à l’application.
* Program.cs : [classe Program](../fundamentals/index.md) qui contient le point d’entrée principal de l’application.
* firstapp.csproj : [fichier projet](https://docs.microsoft.com/dotnet/articles/core/preview3/tools/csproj) le format de fichier projet MSBuild pour les applications ASP.NET Core. Contient des références entre projets, références NuGet et autres éléments connexes du projet.
* appSettings.JSON / appsettings. Development.JSON : Fichier de configuration pour les paramètres application base environnement. [Consultez Configuration](xref:fundamentals/configuration).
* bower.JSON : Bower des dépendances du package pour le projet.
* .bowerrc : fichier de configuration bower qui définit où installer les composants lors du télécharge de Bower actifs.
* bundleconfig.JSON : fichiers de configuration de groupement et réduction des ressources JavaScript et CSS frontales.
* Vues : Contient les vues Razor. Les vues sont les composants qui affichent l’interface utilisateur de l’application (IU). En règle générale, cette interface utilisateur affiche les données du modèle.
* Contrôleurs : Contient les contrôleurs MVC, initialement *HomeController.cs*. Les contrôleurs sont des classes qui gèrent les demandes du navigateur.
* wwwroot : dossier racine de l’application Web.

Pour plus d’informations, consultez [modèle MVC le](xref:mvc/overview).
