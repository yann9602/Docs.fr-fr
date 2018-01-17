Remplacez le contenu du fichier vue Razor *Views/HelloWorld/Index.cshtml* par le code suivant :

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index.cshtml)]

Accédez à `http://localhost:xxxx/HelloWorld`. La méthode `Index` dans `HelloWorldController` n’a pas accompli beaucoup d’actions. Elle a exécuté l’instruction `return View();`, laquelle spécifiait que la méthode doit utiliser un fichier de modèle de vue pour restituer une réponse au navigateur. Étant donné que vous n’avez pas explicitement spécifié le nom du fichier de modèle de vue, MVC a utilisé par défaut le fichier vue *Index.cshtml* présent dans le dossier */Views/HelloWorld*. L’image ci-dessous montre la chaîne « Hello from our View Template! » codée en dur dans la vue.

![Fenêtre du navigateur](../../tutorials/first-mvc-app/adding-view/_static/hell_template.png)

Si votre fenêtre de navigateur est petite (par exemple sur un appareil mobile), vous devrez peut-être activer/désactiver le [bouton de navigation d’amorçage](http://getbootstrap.com/components/#navbar) situé en haut à droite (en appuyant dessus) pour afficher les liens **Accueil**, **À propos de** et **Contact**.

![Fenêtre du navigateur mettant en surbrillance le bouton de navigation d’amorçage](../../tutorials/first-mvc-app/adding-view/_static/1.png)

## <a name="changing-views-and-layout-pages"></a>Changement de vues et de pages de disposition

Appuyez sur les liens du menu (**MvcMovie**, **Accueil**, **À propos de**). Chaque page affiche la même disposition de menu. La disposition du menu est implémentée dans le fichier *Views/Shared/_Layout.cshtml*. Ouvrez le fichier *Views/Shared/_Layout.cshtml*.

Les modèles de [disposition](xref:mvc/views/layout) vous permettent de spécifier la disposition du conteneur HTML de votre site dans un emplacement unique, puis de l’appliquer sur plusieurs pages de votre site. Recherchez la ligne `@RenderBody()`. `RenderBody` est un espace réservé dans lequel toutes les pages spécifiques aux vues que vous créez s’affichent, *encapsulées* dans la page de disposition. Par exemple, si vous sélectionnez le lien **À propos de**, la vue **Views/Home/About.cshtml** est restituée dans la méthode `RenderBody`.

## <a name="change-the-title-and-menu-link-in-the-layout-file"></a>Changer le lien de titre et de menu dans le fichier de disposition

Dans l’élément de titre, remplacez `MvcMovie` par `Movie App`. Remplacez le texte d’ancrage présent dans le modèle de disposition `MvcMovie` par `Mvc Movie` et le contrôleur `Home` par `Movies`, comme illustré ci-dessous :

Remarque : La version ASP.NET Core 2.0 est légèrement différente. Elle ne contient pas `@inject ApplicationInsights` et `@Html.Raw(JavaScriptSnippet.FullScript)`.

[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]

>[!WARNING]
> Comme nous n’avons pas encore implémenté le contrôleur `Movies`, vous obtenez alors une erreur 404 (Introuvable) si vous cliquez sur ce lien.

Enregistrez vos modifications, puis appuyez sur le lien **À propos de**. Notez comment le titre sur l’onglet du navigateur affiche maintenant **À propos de - Movie App** au lieu de **À propos de - Mvc Movie**: 

![Onglet à propos](../../tutorials/first-mvc-app/adding-view/_static/about2.png)

Appuyez sur le lien **Contact** et notez que le titre et le texte d’ancrage affichent également **Movie App**. Nous avons pu effectuer ce changement une fois dans le modèle de disposition et avoir le nouveau texte de lien et le nouveau titre reflétés sur toutes les pages du site.

Examinez le fichier *Views/_ViewStart.cshtml* :


```HTML
@{
    Layout = "_Layout";
}
```

Le fichier *Views/_ViewStart.cshtml* introduit le fichier *Views/Shared/_Layout.cshtml* dans chaque vue. Vous pouvez utiliser la propriété `Layout` pour définir un mode de disposition différent ou lui affecter la valeur `null`. Aucun fichier de disposition n’est donc utilisé.

Changez le titre de la vue `Index`.

Ouvrez *Views/HelloWorld/Index.cshtml*. Il y deux emplacements où un changement doit être apporté :

   * Le texte qui apparaît dans le titre du navigateur.
   * L’en-tête secondaire (élément `<h2>`).

Vous allez les modifier légèrement pour voir quel morceau du code modifie quelle partie de l’application.


```HTML
@{
    ViewData["Title"] = "Movie List";
}

<h2>My Movie List</h2>

<p>Hello from our View Template!</p>
```

Dans le code ci-dessus, `ViewData["Title"] = "Movie List";` définit la propriété `Title` du dictionnaire `ViewData` sur « Movie List ». La propriété `Title` est utilisée dans l’élément HTML `<title>` dans la page de disposition :


```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

Enregistrez votre modification et accédez à `http://localhost:xxxx/HelloWorld`. Notez que le titre du navigateur, l’en-tête principal et les en-têtes secondaires ont changé. (Si vous ne voyez pas les changements dans le navigateur, vous voyez peut-être le contenu mis en cache. Appuyez sur Ctrl+F5 dans votre navigateur pour forcer le chargement de la réponse du serveur.) Le titre du navigateur est créé avec la valeur `ViewData["Title"]` que nous avons définie dans le modèle de vue *Index.cshtml* et la chaîne « - Movie App » ajoutée dans le fichier de disposition.

Notez également comment le contenu du modèle de vue *Index.cshtml* a été fusionné avec le modèle de vue *Views/Shared/_Layout.cshtml* et qu’une seule réponse HTML a été envoyée au navigateur. Les modèles de disposition permettent d’apporter facilement des modifications qui s’appliquent à toutes les pages de votre application. Pour en savoir plus, consultez [Disposition](../../mvc/views/layout.md).

![Vue Movie List](../../tutorials/first-mvc-app/adding-view/_static/hell3.png)

Nos quelques « données » (dans le cas présent, le message « Hello from our View Template! » ) sont toutefois codées en dur. L’application MVC a une vue (« V») et vous avez un contrôleur (« C »), mais pas encore de modèle (« M »).

## <a name="passing-data-from-the-controller-to-the-view"></a>Passage de données du contrôleur vers la vue

Les actions du contrôleur sont appelées en réponse à une demande d’URL entrante. Une classe de contrôleur est l’endroit où vous écrivez le code qui gère les demandes du navigateur entrantes. Le contrôleur récupère les données d’une source de données et détermine le type de réponse à envoyer au navigateur. Il est possible d’utiliser des modèles de vue à partir d’un contrôleur pour générer et mettre en forme une réponse HTML au navigateur.

Les contrôleurs sont chargés de fournir les données nécessaires pour qu’un modèle de vue restitue une réponse. N’oubliez pas que les modèles de vue ne doivent **pas** exécuter de logique métier ou interagir directement avec une base de données. Au lieu de cela, un modèle de vue doit fonctionner uniquement avec les données que le contrôleur lui fournit. Préserver cette « séparation des intérêts » permet de maintenir votre code clair, testable et facile à gérer.

Actuellement, le `Welcome` méthode présente dans la classe `HelloWorldController` prend un paramètre `name` et un paramètre `ID`, puis sort les valeurs directement dans le navigateur. Au lieu que le contrôleur restitue cette réponse sous forme de chaîne, modifions-le pour utiliser plutôt un modèle de vue. Le modèle de vue génère une réponse dynamique, ce qui signifie que vous devez passer les bits de données appropriés du contrôleur à la vue pour générer la réponse. Pour ce faire, le contrôleur doit placer les données dynamiques (paramètres) dont le modèle de vue a besoin dans un dictionnaire `ViewData` auquel le modèle de vue peut ensuite accéder.

Retournez dans le fichier *HelloWorldController.cs* et modifiez la méthode `Welcome` pour ajouter une valeur `Message` et `NumTimes` au dictionnaire `ViewData`. Le dictionnaire `ViewData` est un objet dynamique, ce qui signifie que vous pouvez y placer tout ce que vous voulez ; aucune propriété n’est définie pour l’objet `ViewData` tant que vous ne placez d’élément dedans. Le [système de liaison de données MVC](xref:mvc/models/model-binding) mappe automatiquement les paramètres nommés (`name` et `numTimes`) provenant de la chaîne de requête dans la barre d’adresse aux paramètres de votre méthode. Le fichier *HelloWorldController.cs* complet ressemble à ceci :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

L’objet dictionnaire `ViewData` contient des données qui seront passées à la vue. 

Créez un modèle de vue Welcome nommé *Views/HelloWorld/Welcome.cshtml*.

Vous allez créer une boucle dans le modèle de vue *Welcome.cshtml* qui affiche « Hello » `NumTimes`. Remplacez le contenu de *Views/HelloWorld/Welcome.cshtml* avec le code suivant :

[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Enregistrez vos modifications et accédez à l’URL suivante :

`http://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

Les données sont extraites de l’URL et passées au contrôleur à l’aide du [classeur de modèles MVC](xref:mvc/models/model-binding). Le contrôleur empaquette les données dans un dictionnaire `ViewData` et passe cet objet à la vue. La vue restitue ensuite les données au format HTML dans le navigateur.

![Vue À propose de affichant une étiquette Welcome et l’expression « Hello Rick » affichée quatre fois](../../tutorials/first-mvc-app/adding-view/_static/rick2.png)

Dans l’exemple ci-dessus, nous avons utilisé le dictionnaire `ViewData` pour passer des données du contrôleur à une vue. Plus loin dans ce didacticiel, nous allons faire de même à l’aide d’un modèle de vue. L’approche basée sur le modèle de vue pour passer des données est généralement préférée à l’approche basée sur le dictionnaire `ViewData`. Pour plus d’informations, consultez [Comparaison de ViewModel, ViewData, ViewBag, TempData et Session dans MVC](http://www.mytecbits.com/microsoft/dot-net/viewmodel-viewdata-viewbag-tempdata-mvc).

Il s’agissait d’une sorte de « M » pour le modèle, mais pas d’une base de données en soi. Créons une base de données de films en utilisant ce que nous avons appris.