Ajoutez les propriétés suivantes à la classe `Movie` :

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieNoEF.cs?name=snippet_MovieNoEF)]

Le champ `ID` est requis par la base de données pour la clé primaire.

<a name="dc"></a>
### <a name="add-a-database-context-class"></a>Ajouter une classe de contexte de base de données

Ajoutez une classe dérivée `DbContext` nommée *MovieContext.cs* au dossier *Models*.

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs?range=1-12,14-17,19-21)]

Le code précédent crée une propriété `DbSet` pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table. Le nom de la propriété `DbSet` est `Movies`. Comme la base de données utilise des noms au singulier, l’exemple substitue `OnModelCreating` pour utiliser la forme au singulier (`Movie`) du nom de la table.
