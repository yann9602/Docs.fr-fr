<a name="cs"></a>
### <a name="add-a-database-connection-string"></a>Ajouter une chaîne de connexion de base de données

Ajoutez une chaîne de connexion au fichier *appsettings.json*.

[!code-json[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

<a name="reg"></a>
###  <a name="register-the-database-context"></a>Inscrire le contexte de base de données

Inscrivez le contexte de base de données auprès du conteneur [d’injection de dépendance](xref:fundamentals/dependency-injection) dans le fichier *Startup.cs*.