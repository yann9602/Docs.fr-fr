Ajoutez les propriétés suivantes à la classe `Movie` :

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieNoEF.cs?name=snippet_MovieNoEF)]

Le champ `ID` est requis par la base de données pour la clé primaire.

<a name="dc"></a>
### <a name="add-a-database-context-class"></a>Ajouter une classe de contexte de base de données

Ajoutez une classe dérivée `DbContext` nommée *MovieContext.cs* au dossier *Models*.
[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Models/MovieContext.cs)]

Le code précédent crée une propriété `DbSet` pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.
