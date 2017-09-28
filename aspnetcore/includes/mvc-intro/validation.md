# <a name="adding-validation"></a>Ajout de la validation

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Dans cette section, vous allez ajouter la logique de validation au modèle `Movie`, et vous allez vérifier que les règles de validation sont appliquées chaque fois qu’un utilisateur crée ou modifie un film.

## <a name="keeping-things-dry"></a>Ne vous répétez pas

L’un des principes de conception de MVC est « Ne vous répétez pas » (désigné par l’acronyme [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself), Don’t Repeat Yourself). ASP.NET MVC vous encourage à spécifier les fonctionnalités ou les comportements une seule fois, puis à utiliser la réflexion partout dans une application. Cela réduit la quantité de code à écrire, et rend le code que vous écrivez moins susceptible aux erreurs et plus facile à tester et à gérer.

La prise en charge de la validation fournie par MVC et Entity Framework Core Code First est un bon exemple du principe DRY en action. Vous pouvez spécifier de façon déclarative des règles de validation à un seul emplacement (dans la classe de modèle), et les règles sont appliquées partout dans l’application.

## <a name="adding-validation-rules-to-the-movie-model"></a>Ajout de règles de validation au modèle de film

Ouvrez le fichier *Movie.cs*. DataAnnotations fournit un ensemble intégré d’attributs de validation que vous appliquez de manière déclarative à n’importe quelle classe ou propriété. (Il contient également des attributs de mise en forme comme `DataType` qui aident à effectuer la mise en forme et ne fournissent aucune validation.)

Mettez à jour la classe `Movie` pour tirer parti des attributs de validation intégrés `Required`, `StringLength`, `RegularExpression` et `Range`.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?name=snippet1)]

Les attributs de validation spécifient le comportement que vous souhaitez appliquer sur les propriétés du modèle sur lesquels ils sont appliqués. Les attributs `Required` et `MinimumLength` indiquent qu’une propriété doit avoir une valeur, mais rien n’empêche un utilisateur d’entrer un espace blanc pour satisfaire cette validation. L’attribut `RegularExpression` sert à limiter les caractères pouvant être entrés. Dans le code ci-dessus, `Genre` et `Rating` doivent utiliser uniquement des lettres (les espaces blancs, les chiffres et les caractères spéciaux ne sont pas autorisés). L’attribut `Range` contraint une valeur à une plage spécifiée. L’attribut `StringLength` vous permet de définir la longueur maximale d’une propriété de chaîne, et éventuellement sa longueur minimale. Les types valeur (tels que `decimal`, `int`, `float` et `DateTime`) sont obligatoires par nature et n’ont pas besoin de l’attribut `[Required]`.

L’application automatique des règles de validation par ASP.NET permet d’accroître la fiabilité de votre application. Cela garantit également que vous n’oublierez pas de valider un élément et que vous n’autoriserez pas par inadvertance l’insertion de données incorrectes dans la base de données.

## <a name="validation-error-ui-in-mvc"></a>Interface utilisateur d’erreur de validation dans MVC

Exécutez l’application et accédez au contrôleur Movies.

Appuyez sur le lien **Create New** pour ajouter un nouveau film. Remplissez le formulaire avec des valeurs non valides. Dès que la validation côté client jQuery détecte l’erreur, elle affiche un message d’erreur.

![Formulaire d’affichage de film avec plusieurs erreurs de validation côté client jQuery](../../tutorials/first-mvc-app/validation/_static/val.png)

> [!NOTE]
> Vous ne pourrez peut-être pas entrer des décimales ou des virgules dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que l’anglais qui utilisent une virgule (« , ») comme décimale et des formats de date autres que l’anglais des États-Unis, vous devez effectuer des étapes pour localiser votre application. Pour plus d’informations, consultez [Ressources supplémentaires](#additional-resources). Pour le moment, entrez simplement des nombres entiers tels que 10.

Notez que le formulaire a affiché automatiquement un message d’erreur de validation approprié dans chaque champ contenant une valeur non valide. Les erreurs sont appliquées à la fois côté client (à l’aide de JavaScript et jQuery) et côté serveur (au cas où un utilisateur aurait désactivé JavaScript).

L’un des principaux avantages est que vous n’avez pas eu à changer une seule ligne de code dans la classe `MoviesController` ou dans la vue *Create.cshtml* pour activer cette interface utilisateur de validation. Le contrôleur et les vues créées précédemment dans ce didacticiel ont détecté les règles de validation que vous avez spécifiées à l’aide des attributs de validation sur les propriétés de la classe de modèle `Movie`. Testez la validation à l’aide de la méthode d’action `Edit`. La même validation est appliquée.

Les données de formulaire ne sont pas envoyées au serveur tant qu’il y a des erreurs de validation côté client. Vous pouvez vérifier cela en plaçant un point d’arrêt dans la méthode `HTTP Post`, en utilisant l’[outil Fiddler](http://www.telerik.com/fiddler) ou à l’aide des [Outils de développement F12](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).

## <a name="how-validation-works"></a>Fonctionnement de la validation

Vous vous demandez peut-être comment l’interface utilisateur de validation a été générée sans aucune mise à jour du code dans le contrôleur ou dans les vues. Le code suivant montre les deux méthodes `Create`.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

La première méthode d’action (HTTP GET) `Create` affiche le formulaire de création initial. La deuxième version (`[HttpPost]`) gère la publication de formulaire. La seconde méthode `Create` (la version `[HttpPost]`) appelle `ModelState.IsValid` pour vérifier si le film a des erreurs de validation. L’appel de cette méthode évalue tous les attributs de validation qui ont été appliqués à l’objet. Si l’objet comporte des erreurs de validation, la méthode `Create` réaffiche le formulaire. S’il n’y a pas d’erreur, la méthode enregistre le nouveau film dans la base de données. Dans notre exemple de film, le formulaire n’est pas publié sur le serveur quand des erreurs de validation sont détectées du côté client ; la seconde méthode `Create` n’est jamais appelée quand il y a des erreurs de validation côté client. Si vous désactivez JavaScript dans votre navigateur, la validation client est désactivée et vous pouvez tester la méthode `Create` HTTP POST `ModelState.IsValid` pour détecter les erreurs de validation.

Vous pouvez définir un point d’arrêt dans la méthode `[HttpPost] Create` et vérifier que la méthode n’est jamais appelée. La validation côté client n’enverra pas les données du formulaire quand des erreurs de validation seront détectées. Si vous désactivez JavaScript dans votre navigateur et que vous envoyez ensuite le formulaire avec des erreurs, le point d’arrêt sera atteint. Vous bénéficiez toujours d’une validation complète sans JavaScript. 

L’illustration suivante montre comment désactiver JavaScript dans le navigateur FireFox.

![Firefox : dans Options, sous l’onglet Contenu, décochez la case Activer Javascript.](../../tutorials/first-mvc-app/validation/_static/ff.png)

L’illustration suivante montre comment désactiver JavaScript dans le navigateur Chrome.

![Google Chrome : dans Paramètres de contenu, dans la section Javascript, sélectionnez Interdire à tous les sites d’exécuter JavaScript.](../../tutorials/first-mvc-app/validation/_static/chrome.png)

Après la désactivation de JavaScript, publiez les données non valides et parcourez le débogueur.

![Pendant le débogage d’une publication de données non valides, Intellisense sur ModelState.IsValid indique que la valeur est false.](../../tutorials/first-mvc-app/validation/_static/ms.png)

Voici la partie du modèle de vue *Create.cshtml* pour lequel vous avez généré automatiquement des modèles précédemment dans le didacticiel. Il est utilisé par les méthodes d’action ci-dessus à la fois pour afficher le formulaire initial et pour le réafficher en cas d’erreur.

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Views/Movies/CreateRatingBrevity.cshtml)]

Le [Tag Helper Input](xref:mvc/views/working-with-forms) utilise les attributs [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) et produit les attributs HTML nécessaires à la validation jQuery côté client. Le [Tag Helper Validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) affiche les erreurs de validation. Pour plus d’informations, consultez [Validation](xref:mvc/models/validation).

Le grand avantage de cette approche est que ni le contrôleur ni le modèle de vue `Create` ne savent rien des règles de validation appliquées ou des messages d’erreur affichés. Les règles de validation et les chaînes d’erreur sont spécifiées uniquement dans la classe `Movie`. Ces mêmes règles de validation sont automatiquement appliquées à la vue `Edit` et à tous les autres modèles de vues que vous pouvez créer et qui modifient votre modèle.

Quand vous devez changer la logique de validation, vous pouvez le faire à un seul endroit en ajoutant des attributs de validation au modèle (dans cet exemple, la classe `Movie`). Vous n’aurez pas à vous soucier des différentes parties de l’application qui pourraient être incohérentes avec la façon dont les règles sont appliquées. Toute la logique de validation sera définie à un seul emplacement et utilisée partout. Le code est ainsi très propre, facile à mettre à jour et évolutif. Et cela signifie que vous respecterez entièrement le principe DRY.

## <a name="using-datatype-attributes"></a>Utilisation d’attributs DataType

Ouvrez le fichier *Movie.cs* et examinez la classe `Movie`. L’espace de noms `System.ComponentModel.DataAnnotations` fournit des attributs de mise en forme en plus de l’ensemble intégré d’attributs de validation. Nous avons déjà appliqué une valeur d’énumération `DataType` aux champs de date de sortie et de prix. Le code suivant illustre les propriétés `ReleaseDate` et `Price` avec l’attribut `DataType` approprié.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Les attributs `DataType` fournissent uniquement des conseils permettant au moteur d’affichage de mettre en forme les données (et fournissent des attributs tels que `<a>` pour les URL et `<a href="mailto:EmailAddress.com">` pour l’e-mail). Vous pouvez utiliser l’attribut `RegularExpression` pour valider le format des données. L’attribut `DataType` sert à spécifier un type de données qui est plus spécifique que le type intrinsèque de la base de données ; il ne s’agit pas d’un attribut de validation. Dans le cas présent, nous voulons uniquement effectuer le suivi de la date, et non de l’heure. L’énumération `DataType` fournit de nombreux types de données, telles que Date, Time, PhoneNumber, Currency ou EmailAddress. L’attribut `DataType` peut également permettre à l’application de fournir automatiquement des fonctionnalités propres au type. Par exemple, vous pouvez créer un lien `mailto:` pour `DataType.EmailAddress`, et vous pouvez fournir un sélecteur de date pour `DataType.Date` dans les navigateurs qui prennent en charge HTML5. Les attributs `DataType` émettent des attributs HTML 5 `data-` compréhensibles par les navigateurs HTML 5. Les attributs `DataType` ne fournissent **aucune** validation.

`DataType.Date` ne spécifie pas le format de la date qui s’affiche. Par défaut, le champ de données est affiché conformément aux formats par défaut basés sur le `CultureInfo` du serveur.

L’attribut `DisplayFormat` est utilisé pour spécifier explicitement le format de date :

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Le paramètre `ApplyFormatInEditMode` indique que la mise en forme doit également être appliquée quand la valeur est affichée dans une zone de texte à des fins de modification. (Ceci peut ne pas être souhaitable pour certains champs ; par exemple, pour les valeurs monétaires, vous ne souhaiterez sans doute pas que le symbole monétaire figure dans la zone de texte.)

Vous pouvez utiliser l’attribut `DisplayFormat` par lui-même, mais il est généralement préférable d’utiliser l’attribut `DataType`. L’attribut `DataType` donne la sémantique des données, plutôt que de décrire comment effectuer le rendu sur un écran, et il offre les avantages suivants dont vous ne bénéficiez pas avec DisplayFormat :

* Le navigateur peut activer des fonctionnalités HTML5 (par exemple pour afficher un contrôle de calendrier, le symbole monétaire correspondant aux paramètres régionaux, des liens de messagerie, etc.).

* Par défaut, le navigateur affiche les données à l’aide du format correspondant à vos paramètres régionaux.

* L’attribut `DataType` peut permettre à MVC de choisir le modèle de champ correct pour afficher les données (`DisplayFormat`, utilisé par lui-même, utilise le modèle de chaîne).

> [!NOTE]
> La validation jQuery ne fonctionne pas avec l’attribut `Range` et `DateTime`. Par exemple, le code suivant affiche toujours une erreur de validation côté client, même quand la date se trouve dans la plage spécifiée :

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Vous devez désactiver la validation de date jQuery pour utiliser l’attribut `Range` avec `DateTime`. Il n’est généralement pas recommandé de compiler des dates dures dans vos modèles. Par conséquent, l’utilisation de l’attribut `Range` et de `DateTime` est déconseillée.

Le code suivant illustre la combinaison d’attributs sur une seule ligne :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDAmult.cs?name=snippet1)]

Dans la partie suivante de la série, nous allons examiner l’application et apporter des améliorations aux méthodes `Details` et `Delete` générées automatiquement.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Utilisation des formulaires](xref:mvc/views/working-with-forms)
* [Globalisation et localisation](xref:fundamentals/localization)
* [Introduction aux Tag Helpers](xref:mvc/views/tag-helpers/intro)
* [Création de Tag Helpers](xref:mvc/views/tag-helpers/authoring)
