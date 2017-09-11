Le tableau suivant détaille les paramètres des générateurs de code ASP.NET Core :

| Paramètre               | Description|
| ----------------- | ------------ |
| -m  | Nom du modèle. |
| -dc  | Contexte de données. |
| -udl | Utiliser la disposition par défaut. |
| -outDir | Chemin du dossier de sortie relatif pour créer les vues. |
| --referenceScriptLibraries | Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer. |

Utilisez le commutateur `h` pour obtenir de l’aide sur la commande `aspnet-codegenerator razorpage` :

```console
dotnet aspnet-codegenerator razorpage -h
```
<a name="test"></a>
### <a name="test-the-app"></a>Tester l’application

* Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).
* Testez le lien **Créer**.

 ![Créer une page](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* Testez les liens **Modifier**, **Détails** et **Supprimer**.

Si vous obtenez l’erreur suivante, vérifiez que vous avez exécuté les migrations et mis à jour la base de données :

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)
```