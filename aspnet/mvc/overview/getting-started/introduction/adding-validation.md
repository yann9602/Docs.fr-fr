---
uid: mvc/overview/getting-started/introduction/adding-validation
title: "Ajout d’une Validation | Documents Microsoft"
author: Rick-Anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/17/2013
ms.topic: article
ms.assetid: 9f35ca15-e216-4db6-9ebf-24380b0f31b4
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-validation
msc.type: authoredcontent
ms.openlocfilehash: db09b6c947b219ce21e8f4248dcc6629c6add76c
ms.sourcegitcommit: d1d8071d4093bf2444b5ae19d6e45c3d187e338b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2017
---
<a name="adding-validation"></a>Ajout de la validation
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

[!INCLUDE[Tutorial Note](sample/code-location.md)]

Dans ce cette section vous allez ajouter la logique de validation pour la `Movie` modèle et vous devez vous assurer que les règles de validation sont appliquées à tout moment, un utilisateur tente de créer ou modifier un film à l’aide de l’application.

## <a name="keeping-things-dry"></a>Garder des données sec

Un des principaux les principes de conception d’ASP.NET MVC est [sec](http://en.wikipedia.org/wiki/Don't_repeat_yourself) (&quot;ne pas se répéter&quot;). ASP.NET MVC vous encourage pour spécifier les fonctionnalités ou les comportements qu’une seule fois, puis l’avez partout dans une application. Cela réduit la quantité de code, que vous devez écrire et rend le code que vous écrivez moins source d’erreurs et plus facile à gérer.

La prise en charge de validation fournie par ASP.NET MVC et Entity Framework Code First est un bon exemple du principe sec en action. Vous pouvez spécifier de façon déclarative des règles de validation dans un seul emplacement (dans la classe de modèle) et les règles sont appliquées partout dans l’application.

Examinons à présent comment bénéficier de cette prise en charge de la validation de l’application de film.

## <a name="adding-validation-rules-to-the-movie-model"></a>Ajouter des règles de Validation pour le modèle de film

Vous allez commencer en ajoutant une logique de validation à la `Movie` classe.

Ouvrez le fichier *Movie.cs*. Notez le [ `System.ComponentModel.DataAnnotations` ](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.aspx) espace de noms ne contient pas `System.Web`. DataAnnotations fournit un ensemble intégré d’attributs de validation que vous pouvez appliquer de façon déclarative à une classe ou une propriété. (Il contient également les attributs de mise en forme comme [DataType](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatype.aspx) qui aide à la mise en forme et ne fournissent aucune validation.)

Mettre à jour le `Movie` classe pour tirer parti de la fonction intégrée [ `Required` ](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.requiredattribute.aspx), [ `StringLength` ](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.stringlengthattribute.aspx), [RegularExpression](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx), et [ `Range` ](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.rangeattribute.aspx) attributs de validation. Remplacez la `Movie` classe par le code suivant :

[!code-csharp[Main](adding-validation/samples/sample1.cs?highlight=8,22-24,30-31,37-38)]

Le [ `StringLength` ](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) attribut définit la longueur maximale de la chaîne et il définit cette limitation sur la base de données, par conséquent, le schéma de base de données sera modifié. Cliquez avec le bouton droit sur le **films** table **l’Explorateur de serveurs** et cliquez sur **ouvrir la définition de Table**:

![](adding-validation/_static/image1.png)

Dans l’image ci-dessus, vous pouvez voir tous les champs de chaîne sont définis sur [NVARCHAR (MAX)](https://technet.microsoft.com/en-us/library/ms186939.aspx). Nous allons utiliser les migrations à mettre à jour le schéma. Générez la solution, puis ouvrez le **Package Manager Console** fenêtre et entrez les commandes suivantes :

[!code-console[Main](adding-validation/samples/sample2.cmd)]

Une fois cette commande, Visual Studio ouvre le fichier de classe qui définit la nouvelle `DbMIgration` classe dérivée portant le nom spécifié (`DataAnnotations`), puis, dans le `Up` méthode, vous pouvez visualiser le code qui met à jour les contraintes du schéma :

[!code-csharp[Main](adding-validation/samples/sample3.cs)]

Le `Genre` est de champ ne sont plus nullable (autrement dit, vous devez entrer une valeur). Le `Rating` champ a une longueur maximale de 5 et `Title` a une longueur maximale de 60. La longueur minimale de 3 sur `Title` et la plage sur `Price` n’a pas créé de modifications de schéma.

Examinez le schéma de film :

![](adding-validation/_static/image2.png)

Les champs de chaîne affichent les nouvelles limites de longueur et `Genre` n’est plus vérifiée comme nullable.

Les attributs de validation spécifient le comportement que vous souhaitez appliquer sur les propriétés du modèle sur lesquels ils sont appliqués. Les attributs `Required` et `MinimumLength` indiquent qu’une propriété doit avoir une valeur, mais rien n’empêche un utilisateur d’entrer un espace blanc pour satisfaire cette validation. Le [RegularExpression](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) attribut est utilisé pour limiter les caractères pouvant être entrée. Dans le code ci-dessus, `Genre` et `Rating` doivent utiliser uniquement des lettres (les espaces blancs, les chiffres et les caractères spéciaux ne sont pas autorisés). Le [ `Range` ](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.rangeattribute.aspx) attribut contraint une valeur à dans une plage spécifiée. L’attribut `StringLength` vous permet de définir la longueur maximale d’une propriété de chaîne, et éventuellement sa longueur minimale. Types valeur (tel que `decimal, int, float, DateTime`) sont requis, par nature et n’avez pas besoin du `Required` attribut.

Code vérifie tout d’abord que vous spécifiez sur une classe de modèle de règles de validation sont appliquées avant que l’application enregistre les modifications dans la base de données. Par exemple, le code ci-dessous lèvera une [DbEntityValidationException](https://msdn.microsoft.com/en-us/library/system.data.entity.validation.dbentityvalidationexception(v=vs.103).aspx) exception lorsque la `SaveChanges` méthode est appelée, car plusieurs requis `Movie` les valeurs de propriété sont manquantes :

[!code-csharp[Main](adding-validation/samples/sample4.cs)]

Le code ci-dessus lève l’exception suivante :

*Échec de la validation pour une ou plusieurs entités. Consultez ' entityvalidationerrors ' pour plus de détails.*

Avoir des règles de validation appliquées automatiquement par le .NET Framework permet de rendre votre application plus fiable. Cela garantit également que vous n’oublierez pas de valider un élément et que vous n’autoriserez pas par inadvertance l’insertion de données incorrectes dans la base de données.

## <a name="validation-error-ui-in-aspnet-mvc"></a>Erreur de validation de l’interface utilisateur dans ASP.NET MVC

Exécutez l’application et accédez à la */Movies* URL.

Cliquez sur le **créer un nouveau** pour ajouter un nouveau film. Remplissez le formulaire avec des valeurs non valides. Dès que la validation côté client jQuery détecte l’erreur, elle affiche un message d’erreur.

![8_validationErrors](adding-validation/_static/image3.png)

> [!NOTE]
> pour prendre en charge la validation jQuery pour les paramètres régionaux non anglais qui utilisent une virgule («, ») pour un point décimal, vous devez inclure NuGet globaliser comme décrit précédemment dans ce didacticiel.


Notez comment le formulaire a automatiquement utilisé une couleur de bordure rouge pour mettre en évidence les zones de texte qui contiennent des données non valides et a émis un message d’erreur de validation appropriées en regard de chacun d’eux. Les erreurs sont appliquées à la fois côté client (à l’aide de JavaScript et jQuery) et côté serveur (au cas où un utilisateur aurait désactivé JavaScript).

Un avantage est que vous n’avez pas devez modifier une seule ligne de code dans le `MoviesController` classe ou dans le *Create.cshtml* vue afin d’activer cette validation de l’interface utilisateur. Le contrôleur et les vues créées précédemment dans ce didacticiel ont détecté les règles de validation que vous avez spécifiées à l’aide des attributs de validation sur les propriétés de la classe de modèle `Movie`. Testez la validation à l’aide de la méthode d’action `Edit`. La même validation est appliquée.

Les données de formulaire ne sont pas envoyées au serveur tant qu’il y a des erreurs de validation côté client. Vous pouvez vérifier cela en plaçant un point d’arrêt dans la méthode HTTP Post, à l’aide de la [outil fiddler](http://fiddler2.com/fiddler2/), ou Internet Explorer [outils de développement F12](https://msdn.microsoft.com/en-us/ie/aa740478).

## <a name="how-validation-occurs-in-the-create-view-and-create-action-method"></a>La Validation se produit de la créer afficher et créer la méthode d’Action

Vous vous demandez peut-être comment l’interface utilisateur de validation a été générée sans aucune mise à jour du code dans le contrôleur ou dans les vues. La liste suivante indique ce que le `Create` méthodes dans le `MovieController` ressemble de classe. Ils sont identiques à la façon dont vous les avez créés précédemment dans ce didacticiel.

[!code-csharp[Main](adding-validation/samples/sample5.cs)]

La première méthode d’action (HTTP GET) `Create` affiche le formulaire de création initial. La deuxième version (`[HttpPost]`) gère la publication de formulaire. La seconde méthode `Create` (la version `HttpPost`) appelle `ModelState.IsValid` pour vérifier si le film a des erreurs de validation. L’appel de cette méthode évalue tous les attributs de validation qui ont été appliqués à l’objet. Si l’objet comporte des erreurs de validation, la méthode `Create` réaffiche le formulaire. S’il n’y a pas d’erreur, la méthode enregistre le nouveau film dans la base de données. Dans notre exemple de film, **le formulaire n’est pas publié sur le serveur lorsqu’il existe des erreurs de validation détectées sur le côté client ; le deuxième** `Create` **méthode n’est jamais appelée**. Si vous désactivez JavaScript dans votre navigateur, la validation côté client est désactivée et la requête HTTP POST `Create` les appels de méthode `ModelState.IsValid` pour vérifier si le film a des erreurs de validation.

Vous pouvez définir un point d’arrêt dans la méthode `HttpPost Create` et vérifier que la méthode n’est jamais appelée. La validation côté client n’enverra pas les données du formulaire quand des erreurs de validation seront détectées. Si vous désactivez JavaScript dans votre navigateur et que vous envoyez ensuite le formulaire avec des erreurs, le point d’arrêt sera atteint. Vous bénéficiez toujours d’une validation complète sans JavaScript. L’illustration suivante montre comment désactiver JavaScript dans Internet Explorer.

![](adding-validation/_static/image5.png)

![](adding-validation/_static/image6.png)

L’illustration suivante montre comment désactiver JavaScript dans le navigateur FireFox.

![](adding-validation/_static/image7.png)

L’illustration suivante montre comment désactiver JavaScript dans le navigateur Chrome.

![](adding-validation/_static/image8.png)

Voici le *Create.cshtml* afficher le modèle que vous structuré précédemment dans le didacticiel. Il est utilisé par les méthodes d’action ci-dessus à la fois pour afficher le formulaire initial et pour le réafficher en cas d’erreur.

[!code-cshtml[Main](adding-validation/samples/sample6.cshtml?highlight=16-17)]

Notez comment le code utilise un `Html.EditorFor` application d’assistance pour générer le `<input>` élément pour chaque `Movie` propriété. En regard de ce programme d’assistance est un appel à la `Html.ValidationMessageFor` méthode d’assistance. Ces deux méthodes d’assistance de travail avec l’objet de modèle qui est passé par le contrôleur à la vue (dans ce cas, un `Movie` objet). Ils recherchent automatiquement des attributs de validation spécifiés sur les messages d’erreur de modèle et l’affichage comme il convient.

Le grand avantage de cette approche est que ni le contrôleur ni le modèle de vue `Create` ne savent rien des règles de validation appliquées ou des messages d’erreur affichés. Les règles de validation et les chaînes d’erreur sont spécifiées uniquement dans la classe `Movie`. Ces mêmes règles de validation sont automatiquement appliquées à la vue `Edit` et à tous les autres modèles de vues que vous pouvez créer et qui modifient votre modèle.

Si vous souhaitez modifier la logique de validation par la suite, vous pouvez le faire dans un seul endroit en ajoutant des attributs de validation pour le modèle (dans cet exemple, la `movie` classe). Vous n’aurez pas à vous soucier des différentes parties de l’application qui pourraient être incohérentes avec la façon dont les règles sont appliquées. Toute la logique de validation sera définie à un seul emplacement et utilisée partout. Le code est ainsi très propre, facile à mettre à jour et évolutif. Et il signifie que vous allez être entièrement en respectant le *sec* principe.

## <a name="using-datatype-attributes"></a>Utilisation d’attributs DataType

Ouvrez le fichier *Movie.cs* et examinez la classe `Movie`. Le [ `System.ComponentModel.DataAnnotations` ](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.aspx) espace de noms fournit des attributs de mise en forme en plus de l’ensemble intégré d’attributs de validation. Nous avons déjà appliqué un [ `DataType` ](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatype.aspx) valeur d’énumération pour la date de publication et les champs de prix. Le code suivant illustre la `ReleaseDate` et `Price` propriétés appropriés [ `DataType` ](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatype.aspx) attribut.

[!code-csharp[Main](adding-validation/samples/sample7.cs)]

Le [DataType](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatypeattribute.aspx) uniquement, les attributs fournissent des conseils pour le moteur d’affichage formater les données (et fournir des attributs tels que `<a>` pour l’URL et `<a href="mailto:EmailAddress.com">` pour le courrier électronique. Vous pouvez utiliser la [RegularExpression](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) attribut à valider le format des données. Le [DataType](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatypeattribute.aspx) attribut est utilisé pour spécifier un type de données qui est plus spécifique que le type intrinsèque de la base de données, elles sont ***pas*** attributs de validation. Dans ce cas, nous voulons uniquement le suivi de la date, pas la date et l’heure. Le [énumération DataType](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatype.aspx) fournit de nombreux types de données, tels que *Date, heure, numéro de téléphone, devise, EmailAddress* et bien plus encore. L’attribut `DataType` peut également permettre à l’application de fournir automatiquement des fonctionnalités propres au type. Par exemple, un `mailto:` lien peut être créé pour [DataType.EmailAddress](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatype.aspx), et un sélecteur de date peut être fourni pour [DataType.Date](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatype.aspx) dans les navigateurs qui prennent en charge [HTML5](http://html5.org/). Le [type de données](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatypeattribute.aspx) émet des attributs HTML 5 [données -](http://ejohn.org/blog/html-5-data-attributes/) (prononcé *data tiret*) les attributs que les navigateurs HTML 5 peuvent comprendre. Le [DataType](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatypeattribute.aspx) attributs ne fournissent pas de validation.

`DataType.Date` ne spécifie pas le format de la date qui s’affiche. Par défaut, le champ de données s’affiche selon les formats par défaut basés sur le serveur[CultureInfo](https://msdn.microsoft.com/en-us/library/vstudio/system.globalization.cultureinfo(v=vs.110).aspx).

L’attribut `DisplayFormat` est utilisé pour spécifier explicitement le format de date :


[!code-csharp[Main](adding-validation/samples/sample8.cs)]


Le `ApplyFormatInEditMode` paramètre spécifie que la mise en forme spécifiée doit également être appliquée lorsque la valeur est affichée dans une zone de texte pour la modification. (Vous pouvez que pour certains champs, par exemple, pour les valeurs de devise, vous pouvez le symbole de devise dans la zone de texte pour le modifier.)

Vous pouvez utiliser la [DisplayFormat](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.displayformatattribute.aspx) attribut par lui-même, mais il est généralement judicieux d’utiliser le [DataType](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatypeattribute.aspx) également d’attribut. Le `DataType` attribut transmet le *sémantique* des données en opposition à comment effectuer le rendu sur un écran et offre les avantages suivants que vous n’obtenez pas avec `DisplayFormat`:

- Le navigateur peut activer des fonctionnalités HTML5 (par exemple afficher un contrôle de calendrier, le symbole monétaire approprié de paramètres régionaux, des liens de messagerie, etc.).
- Par défaut, le navigateur renvoie les données en utilisant le format approprié en fonction de votre[paramètres régionaux](https://msdn.microsoft.com/en-us/library/vstudio/wyzd2bce.aspx).
- Le[DataType](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatypeattribute.aspx) attribut peut activer MVC choisir le modèle de champ de droite pour afficher les données (la [DisplayFormat](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.displayformatattribute.aspx) si utilisé par lui-même utilise le modèle de chaîne). Pour plus d’informations, consultez de Brad Wilson[modèles ASP.NET MVC 2](http://bradwilson.typepad.com/blog/2009/10/aspnet-mvc-2-templates-part-1-introduction.html). (Bien que rédigées pour MVC 2, cet article toujours s’applique à la version actuelle d’ASP.NET MVC.)

Si vous utilisez la `DataType` attribut avec un champ de date, vous devez spécifier le `DisplayFormat` attribut également afin de garantir que le champ s’affiche correctement dans les navigateurs de Chrome. Pour plus d’informations, consultez [ce thread StackOverflow](http://stackoverflow.com/questions/12633471/mvc4-datatype-date-editorfor-wont-display-date-value-in-chrome-fine-in-ie).

> [!NOTE]
> jQuery validation ne fonctionne pas avec le[plage](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.rangeattribute.aspx) attribut et[DateTime](https://msdn.microsoft.com/en-us/library/system.datetime.aspx). Par exemple, le code suivant affiche toujours une erreur de validation côté client, même quand la date se trouve dans la plage spécifiée :
> 
> [!code-csharp[Main](adding-validation/samples/sample9.cs)]
> 
> Vous devez désactiver la validation de date jQuery à utiliser le [plage](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.rangeattribute.aspx) d’attribut avec[DateTime](https://msdn.microsoft.com/en-us/library/system.datetime.aspx). Il n’est généralement pas recommandé pour compiler les dates durs dans vos modèles, à l’aide de la[plage](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.rangeattribute.aspx) attribut et[DateTime](https://msdn.microsoft.com/en-us/library/system.datetime.aspx) est déconseillée.


Le code suivant illustre la combinaison d’attributs sur une seule ligne :

[!code-csharp[Main](adding-validation/samples/sample10.cs?highlight=6,10)]

Dans la partie suivante de la série, nous allons examiner l’application et apporter des améliorations aux méthodes `Details` et `Delete` générées automatiquement.

>[!div class="step-by-step"]
[Précédent](adding-a-new-field.md)
[Suivant](examining-the-details-and-delete-methods.md)
