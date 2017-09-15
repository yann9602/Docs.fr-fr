## <a name="register-the-database-context"></a>Inscrire le contexte de base de données

Pour pouvoir insérer le contexte de base de données dans le contrôleur, nous devons l’inscrire auprès du conteneur [d’injection de dépendance](xref:fundamentals/dependency-injection). Inscrivez le contexte de base de données auprès du conteneur de service à l’aide de la prise en charge intégrée de [l’injection de dépendance](xref:fundamentals/dependency-injection). Remplacez le contenu du fichier *Startup.cs* par ceci :

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Startup.cs?highlight=2,4,12)]

Le code précédent :

* Supprime le code que nous n’utilisons pas.
* Spécifie qu’une base de données en mémoire est injectée dans le conteneur de service.
