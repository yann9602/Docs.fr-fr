Remplacez le contenu de *Controllers/HelloWorldController.cs* par ceci :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Chaque méthode `public` d’un contrôleur peut être appelée en tant que point de terminaison HTTP. Dans l’exemple ci-dessus, les deux méthodes retournent une chaîne.  Notez les commentaires qui précèdent chaque méthode.

Un point de terminaison HTTP est une URL qui peut être ciblée dans l’application web, comme `http://localhost:1234/HelloWorld`, et qui combine le protocole utilisé : `HTTP`, l’emplacement réseau du serveur web (avec le port TCP) : `localhost:1234` et l’URI cible `HelloWorld`.

Le premier commentaire indique qu’il s’agit d’une méthode [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) qui est appelée en ajoutant « /HelloWorld/ » à l’URL de base. Le second commentaire spécifie qu’il s’agit d’une méthode [HTTP GET](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) qui est appelée en ajoutant « /HelloWorld/Welcome/ » à l’URL. Plus loin dans ce didacticiel, vous utilisez le moteur de génération de modèles automatique pour générer des méthodes `HTTP POST`.

Exécutez l’application en mode de non-débogage et ajoutez « HelloWorld » au chemin dans la barre d’adresse. La méthode `Index` retourne une chaîne.

![Fenêtre de navigateur montrant une réponse de l’application à l’action This is my default](../../tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC appelle les classes du contrôleur (et les méthodes d’action au sein de celles-ci) en fonction de l’URL entrante. La [logique de routage d’URL](../../mvc/controllers/routing.md) par défaut utilisée par le modèle MVC utilise un format comme celui-ci pour déterminer le code à appeler :

`/[Controller]/[ActionName]/[Parameters]`

Vous définissez le format pour le routage dans la méthode `Configure` du fichier *Startup.cs*.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

Quand vous exécutez l’application et que vous ne fournissez aucun segment d’URL, sa valeur par défaut est le contrôleur « Home » et la méthode « Index » spécifiée dans la ligne du modèle mise en surbrillance ci-dessus.

Le premier segment d’URL détermine la classe du contrôleur à exécuter. Ainsi, `localhost:xxxx/HelloWorld` est mappé à la classe `HelloWorldController`. La seconde partie du segment d’URL détermine la méthode d’action sur la classe. Ainsi, `localhost:xxxx/HelloWorld/Index` provoque l’exécution de la méthode `Index` de la classe `HelloWorldController`. Notez que vous n’avez eu qu’à accéder à `localhost:xxxx/HelloWorld` pour que la méthode `Index` soit appelée par défaut. La raison en est que `Index` est la méthode par défaut qui est appelée sur un contrôleur si un nom de méthode n’est pas explicitement spécifié. La troisième partie du segment d’URL (`id`) concerne les données de routage. Les données de routage sont présentées plus loin dans ce didacticiel.

Accédez à `http://localhost:xxxx/HelloWorld/Welcome`. La méthode `Welcome` s’exécute et retourne la chaîne « This is the Welcome action method... ». Pour cette URL, le contrôleur est `HelloWorld`, et `Welcome` est la méthode d’action. Vous n’avez pas encore utilisé la partie `[Parameters]` de l’URL.

![Fenêtre de navigateur montrant une réponse de la méthode d’action « This is the Welcome action »](../../tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Modifiez le code pour passer des informations sur les paramètres de l’URL au contrôleur. Par exemple, `/HelloWorld/Welcome?name=Rick&numtimes=4`. Modifiez la méthode `Welcome` en y incluant les deux paramètres, comme indiqué dans le code suivant. 

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Le code précédent :

* Utilise la fonctionnalité de paramètre facultatif de C# pour indiquer que le paramètre `numTimes` a 1 comme valeur par défaut si aucune valeur n’est passée pour ce paramètre.
* Utilise `HtmlEncoder.Default.Encode` pour protéger l’application des entrées malveillantes (à savoir JavaScript). 
* Utilise des [chaînes interpolées](https://docs.microsoft.com/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)

Exécutez votre application et accédez à :

   `http://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

(Remplacez xxxx par votre numéro de port.) Vous pouvez essayer différentes valeurs pour `name` et `numtimes` dans l’URL. Le système de [liaison de données](../../mvc/models/model-binding.md) du modèle MVC mappe automatiquement les paramètres nommés provenant de la chaîne de requête dans la barre d’adresse aux paramètres de votre méthode. Pour plus d’informations, consultez [Liaison de données](../../mvc/models/model-binding.md).

![Fenêtre de navigateur montrant une réponse de l’application « Hello Rick, NumTimes is: 4 »](../../tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Dans l’image ci-dessus, le segment d’URL (`Parameters`) n’est pas utilisé, les paramètres `name` et `numTimes` sont passés en tant que [chaînes de requête](https://wikipedia.org/wiki/Query_string). Le `?` (point d’interrogation) dans l’URL ci-dessus est un séparateur, qui est suivi des chaînes de requête. Le caractère `&` sépare les chaînes de requête.

Remplacez la méthode `Welcome` par le code suivant :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Exécutez l’application et entrez l’URL suivante : `http://localhost:xxx/HelloWorld/Welcome/3?name=Rick`

![Fenêtre de navigateur montrant une réponse de l’application « Hello Rick, ID: 3 »](../../tutorials/first-mvc-app/adding-controller/_static/rick_routedata.png)

Cette fois, le troisième segment de l’URL correspondait au paramètre de routage `id`. La méthode `Welcome` contient un paramètre `id` qui correspondait au modèle d’URL de la méthode `MapRoute`. Le `?` de fin (dans `id?`) indique que le paramètre `id` est facultatif.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

Dans ces exemples, le contrôleur a fait la partie « VC » du modèle MVC, autrement dit, la vue et le contrôleur fonctionnent. Le contrôleur retourne directement du HTML. En règle générale, vous ne souhaitez pas que les contrôleurs retournent directement du HTML, car le codage et la maintenance deviennent dans ce cas très laborieux. Au lieu de cela, vous utilisez généralement un fichier de modèle de vue Razor distinct pour faciliter la génération de la réponse HTML. Vous faites cela dans le didacticiel suivant.
