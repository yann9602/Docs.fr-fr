---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/form-basics
title: "Présentation des Pages Web ASP.NET - notions de base de formulaire HTML | Documents Microsoft"
author: tfitzmac
description: "Ce didacticiel vous montre les principes fondamentaux de la création d’un formulaire d’entrée et comment gérer l’entrée d’utilisateur lorsque vous utilisez les Pages Web ASP.NET (Razor). Et maintenant que vous avez..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/28/2015
ms.topic: article
ms.assetid: 81ed82bf-b940-44f1-b94a-555d0cb7cc98
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/form-basics
msc.type: authoredcontent
ms.openlocfilehash: 68056759b2e80230e5fd2c0f9b2d2a89b549cf37
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="introducing-aspnet-web-pages---html-form-basics"></a>Présentation des Pages Web ASP.NET - notions de base de formulaire HTML
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Ce didacticiel vous montre les principes fondamentaux de la création d’un formulaire d’entrée et comment gérer l’entrée d’utilisateur lorsque vous utilisez les Pages Web ASP.NET (Razor). Et maintenant que vous avez une base de données, vous allez utiliser vos compétences en matière de formulaire pour permettre aux utilisateurs de rechercher des films spécifiques dans la base de données. Il suppose que vous avez terminé la série via [Introduction à afficher à l’aide de ASP.NET Web Pages de données](/aspnet/web-pages/overview/getting-started/introducing-aspnet-web-pages-2/displaying-data).
> 
> Ce que vous allez apprendre :
> 
> - Comment créer un formulaire à l’aide des éléments HTML standards.
> - Dans un formulaire d’entrée de la lecture de l’utilisateur.
> - Comment créer une requête SQL que sélectivement Obtient des données à l’aide d’une recherche de terme que l’utilisateur fournit.
> - Comment les champs dans la page « se souviennent » de l’entrée utilisateur.
>   
> 
> Fonctionnalités technologies abordées :
> 
> - Objet `Request`.
> - L’instruction SQL `Where` clause.


## <a name="what-youll-build"></a>Ce que vous allez générer

Dans le didacticiel précédent, vous créé une base de données, ajouté des données à ce dernier, puis utilisé le `WebGrid` application d’assistance pour afficher les données. Dans ce didacticiel, vous allez ajouter une zone de recherche qui vous permet de rechercher des vidéos d’un genre spécifique ou dont le titre contient le mot. (Par exemple, vous serez en mesure de trouver toutes les vidéos qui appartiennent au genre est « Action » ou dont le titre contient « Harry » ou « Adventure ».)

Lorsque vous avez terminé ce didacticiel, vous avez une page similaire à celle-ci :

![Page de films avec Genre et titre de la recherche](form-basics/_static/image1.png)

La partie de la liste de la page est identique à celle du dernier didacticiel &mdash; une grille. La différence sera que la grille affichera uniquement les films que vous recherchez.

## <a name="about-html-forms"></a>Sur les formulaires HTML

(Si vous avez une expérience de création de formulaires HTML et la différence entre `GET` et `POST`, vous pouvez ignorer cette section.)

Un formulaire dispose d’éléments d’entrée utilisateur &mdash; zones de texte, boutons, cases d’option, les cases à cocher, listes déroulantes et ainsi de suite. Les utilisateurs remplissez ces contrôles ou effectuer des sélections et ensuite envoyer le formulaire en cliquant sur un bouton.

La syntaxe HTML de base d’un formulaire est illustrée dans cet exemple :

[!code-html[Main](form-basics/samples/sample1.html)]

Lorsque cette balise s’exécute dans une page, il crée un formulaire simple qui ressemble à cette illustration :

![Base formulaire HTML en tant que de rendu dans le navigateur](form-basics/_static/image2.png)

Le `<form>` élément entoure des éléments HTML à être soumis. (Une erreur facile qui consiste à effectuer consiste à ajouter des éléments à la page en omettant puis les placer à l’intérieur d’un `<form>` élément. Dans ce cas, rien n’est envoyée.) Le `method` attribut indique au navigateur comment envoyer l’entrée d’utilisateur. Vous affectez la valeur `post` si vous effectuez une mise à jour sur le serveur ou `get` si vous êtes simplement récupérer les données à partir du serveur.

<a id="GET,_POST,_and_HTTP_Verb_Safety"></a>

> [!TIP] 
> 
> **GET, POST et sécurité du verbe HTTP**
> 
> Navigateurs et les serveurs utilisent pour échanger des informations, le protocole HTTP est particulièrement simple dans ses opérations de base. Navigateurs utilisent uniquement quelques verbes pour effectuer des demandes aux serveurs. Lorsque vous écrivez du code pour le web, il est utile de comprendre ces verbes et comment le navigateur et le serveur les utilisent. Et loin les verbes couramment utilisées sont ces :
> 
> - `GET`. Le navigateur utilise ce verbe pour extraire un élément à partir du serveur. Par exemple, lorsque vous tapez une URL dans votre navigateur, le navigateur effectue une `GET` opération visant à demander la page. Si la page comprend des graphiques, le navigateur effectue supplémentaires `GET` opérations pour obtenir les images. Si le `GET` opération a passer des informations sur le serveur, les informations sont transmises en tant que partie de l’URL dans la chaîne de requête.
> - `POST`. Le navigateur envoie un `POST` demande afin d’envoyer des données pour être ajouté ou modifié sur le serveur. Par exemple, le `POST` verbe est utilisé pour créer des enregistrements dans une base de données ou modifier existants. La plupart du temps, lorsque vous remplissez un formulaire et cliquez sur le bouton Soumettre, le navigateur effectue une `POST` opération. Dans un `POST` opération, les données transmises sur le serveur sont dans le corps de la page.
> 
> Une distinction importante entre ces verbes est qu’un `GET` opération n’est pas censé modifie en rien sur le serveur, ou pour placer de manière légèrement plus abstraite, un `GET` opération n’entraîne pas une modification d’état sur le serveur. Vous pouvez effectuer une `GET` opération sur les mêmes ressources qu’autant de fois que vous le souhaitez et ne modifiez pas ces ressources. (A `GET` opération on dit souvent « sécurisée », ou pour utiliser un terme technique, est *idempotent*.) En revanche, bien entendu, un `POST` demande quelque chose sur le serveur change chaque fois que vous exécutez l’opération.
> 
> Deux exemples permet d’illustrer cette distinction. Lorsque vous effectuez une recherche à l’aide d’un moteur tel que Bing ou Google, vous remplissez un formulaire qui se compose d’une zone de texte, puis cliquez le bouton de recherche. Le navigateur effectue une `GET` opération, avec la valeur que vous avez entré dans la zone passée en tant que partie de l’URL. À l’aide un `GET` opération pour ce type de formulaire est parfait, car une opération de recherche ne change pas toutes les ressources sur le serveur, il récupère uniquement les informations.
> 
> Considérez maintenant le processus de commande en ligne. Vous renseignez les détails de commande et puis cliquez sur le bouton Envoyer. Cette opération doit être un `POST` demande, car l’opération entraîne des modifications sur le serveur, comme un nouvel enregistrement de commande, une modification de vos informations de compte et peut-être nombreuses autres modifications. Contrairement à la `GET` opération, vous ne pouvez pas répéter votre `POST` demande — si vous l’avez fait, chaque fois que vous soumis à nouveau la demande, vous génère une nouvelle commande sur le serveur. (Dans ce cas, des sites Web vous avertit souvent ne pas à cliquez plusieurs fois sur un bouton d’envoi ou désactive le bouton d’envoi afin que vous ne soumettez à nouveau le formulaire par inadvertance.)
> 
> Au cours de ce didacticiel, vous allez utiliser à la fois un `GET` opération et une `POST` opération pour travailler avec des formulaires HTML. Nous allons expliquer dans chaque cas pourquoi le verbe que vous utilisez est celui qui convient.
> 
> (Pour en savoir plus sur les verbes HTTP, consultez la [des définitions de méthode](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) article sur le site W3C.)


La plupart des éléments d’entrée d’utilisateur sont HTML `<input>` éléments. Ils ressemblent `<input type="type" name="name">,` où *type* indique le type de contrôle d’entrée d’utilisateur souhaité. Ces éléments sont ceux courantes :

- Zone de texte :`<input type="text">`
- Case à cocher :`<input type="check">`
- Case d’option :`<input type="radio">`
- Bouton :`<input type="button">`
- Bouton Envoyer :`<input type="submit">`

Vous pouvez également utiliser le `<textarea>` élément pour créer une zone de texte multiligne et `<select>` élément pour créer une liste déroulante ou une liste déroulante. (Pour plus d’informations sur HTML forment des éléments, consultez [formulaires HTML et entrée](http://www.w3schools.com/html/html_forms.asp) sur le site W3Schools.)

Le `name` attribut est très important, car le nom est comment vous allez obtenir la valeur de l’élément dans une version ultérieure, comme vous le verrez bientôt.

La partie intéressante est, le développeur de la page opérations avec l’entrée d’utilisateur. Il n’existe aucun comportement intégré associé à ces éléments. Au lieu de cela, vous devez obtenir les valeurs que l’utilisateur a entré ou sélectionné et faire quelque chose. C’est ce que vous allez apprendre dans ce didacticiel.

> [!TIP] 
> 
> **HTML5 et les formulaires de saisie**
> 
> Comme vous le savez peut-être, HTML est en transition et la version la plus récente (HTML5) inclut la prise en charge de manière plus intuitive pour les utilisateurs à entrer des informations. Par exemple, en HTML5, vous (le développeur de pages) peut indiquer la page que vous souhaitez que l’utilisateur pour entrer une date. Le navigateur peut afficher automatiquement puis d’un calendrier plutôt que d’exiger de l’utilisateur à entrer une date manuellement. Toutefois, HTML5 est nouveau et le n'est pas encore pris en charge dans tous les navigateurs.
> 
> Les Pages Web ASP.NET prend en charge HTML5 d’entrée dans la mesure où l’Explorateur de l’utilisateur. Pour avoir une idée de nouveaux attributs de la `<input>` élément en HTML5, consultez [HTML &lt;d’entrée&gt; attribut type](http://www.w3schools.com/html/html_form_input_types.asp) sur le site W3Schools.


## <a name="creating-the-form"></a>Création du formulaire

Dans WebMatrix, dans le **fichiers** espace de travail, ouvrez le *Movies.cshtml* page.

Après la fermeture `</h1>` balise et avant l’ouverture `<div>` balise de le `grid.GetHtml` appeler, ajoutez le balisage suivant :

[!code-html[Main](form-basics/samples/sample2.html)]

Cette balise crée un formulaire qui comprend une zone de texte nommée `searchGenre` et un bouton d’envoi. Le texte de zone et d’envoyer le bouton sont placées dans un `<form>` élément dont `method` attribut a la valeur `get`. (N’oubliez pas que si vous ne placez la zone de texte et les soumettre à l’intérieur d’un `<form>` élément, rien ne sera envoyé lorsque vous cliquez sur le bouton.) Vous utilisez la `GET` verbe ici, car vous créez un formulaire qui n’apporte aucune modification sur le serveur, il permet simplement de rechercher. (Dans le didacticiel précédent, vous avez utilisé un `post` (méthode), qui est la manière dont vous soumettez des modifications sur le serveur. Vous verrez que dans le didacticiel suivant à nouveau.)

Exécutez la page. Bien que vous n’avez pas défini un comportement pour le formulaire, vous pouvez voir à quoi elle ressemble :

![Page de films avec zone de recherche pour Genre](form-basics/_static/image3.png)

Entrez une valeur dans la zone de texte, tels que « Comédie ». Puis cliquez sur **recherche Genre**.

Prenez note de l’URL de la page. Étant donné que vous définissez la `<form>` l’élément `method` attribut `get`, la valeur que vous avez entré fait désormais partie de la chaîne de requête dans l’URL, comme suit :

`http://localhost:45661/Movies.cshtml?searchGenre=Comedy`

## <a name="reading-form-values"></a>Lecture de valeurs de formulaire

La page contient déjà du code qui obtient des données de la base de données et affiche les résultats dans une grille. Vous devez maintenant ajouter du code qui lit la valeur de la zone de texte pour vous pouvez d’exécuter une requête SQL qui inclut le terme de recherche.

Étant donné que vous définissez la méthode du formulaire `get`, vous pouvez lire la valeur qui a été entrée dans la zone de texte à l’aide de code comme suit :

`var searchTerm = Request.QueryString["searchGenre"];`

Le `Request.QueryString` objet (la `QueryString` propriété de la `Request` objet) inclut les valeurs des éléments qui ont été soumis dans le cadre de la `GET` opération. Le `Request.QueryString` propriété contient un *collection* (liste) des valeurs qui sont envoyées dans le formulaire. Pour obtenir une valeur individuelle, vous spécifiez le nom de l’élément que vous voulez. C’est pourquoi vous devez avoir un `name` de l’attribut le `<input>` élément (`searchTerm`) qui crée la zone de texte. (Pour plus d’informations sur la `Request` d’objets, consultez la [encadré](#BKMK_TheRequestObject) plus tard.)

Il est suffisamment simple pour lire la valeur de la zone de texte. Toutefois, si l’utilisateur n’entrez rien du tout dans la zone de texte mais cliqué sur **recherche** tout de même, vous pouvez ignorer ce clic, car il n’existe aucun élément à rechercher.

Le code suivant est un exemple qui montre comment implémenter ces conditions. (Vous n’êtes pas obligé d’ajouter ce code encore, vous devez le faire dans un instant.)

[!code-csharp[Main](form-basics/samples/sample3.cs)]

Le test s’arrête vers le bas de cette façon :

- Obtenir la valeur de `Request.QueryString["searchGenre"]`, à savoir la valeur qui a été entrée dans le `<input>` élément nommé `searchGenre`.
- Déterminer si elle est vide à l’aide de la `IsEmpty` (méthode). Cette méthode est la méthode standard pour déterminer si un élément (par exemple, un élément de formulaire) contient une valeur. Mais, vous peu uniquement s’il a *pas* vide, par conséquent...
- Ajouter le `!` opérateur devant le `IsEmpty` de test. (Le `!` opérateur signifie NOT logique).

En clair, l’ensemble `if` condition se traduit par les éléments suivants : *si l’élément de searchGenre du formulaire n’est pas vide, puis...*

Ce bloc définit l’étape de création d’une requête qui utilise le terme de recherche. Vous devez le faire dans la section suivante.

<a id="BKMK_TheRequestObject"></a>

> [!TIP] 
> 
> **L’objet de demande**
> 
> Le `Request` objet contient toutes les informations que le navigateur envoie à votre application lorsqu’une page est demandée ou envoyée. Cet objet consacrée les informations fournies par l’utilisateur, telles que les valeurs de zone de texte ou un fichier à télécharger. Il inclut également toutes sortes d’informations supplémentaires, comme les cookies, les valeurs dans la chaîne de requête URL (le cas échéant), le chemin d’accès de fichier de la page qui est en cours d’exécution, le type de navigateur à l’aide de l’utilisateur, la liste des langues qui sont définies dans le navigateur et bien plus encore.
> 
> Le `Request` objet est un *collection* (liste) de valeurs. Vous obtenez une valeur individuelle dans la collection en spécifiant son nom :
> 
> `var someValue = Request["name"];`
> 
> Le `Request` objet expose réellement plusieurs sous-ensembles. Exemple :
> 
> - `Request.Form`vous donne les valeurs à partir des éléments à l’intérieur de l’élément soumis `<form>` élément si la demande est un `POST` demande.
> - `Request.QueryString`permet que les valeurs dans la chaîne de requête URL. (Dans une URL telle que `http://mysite/myapp/page?searchGenre=action&page=2`, le `?searchGenre=action&page=2` section de l’URL est la chaîne de requête.)
> - `Request.Cookies`collection vous permet d’accéder aux cookies envoyées par le navigateur.
> 
> Pour obtenir une valeur qui est dans le formulaire envoyé, vous pouvez utiliser `Request["name"]`. Vous pouvez également utiliser les versions plus spécifiques `Request.Form["name"]` (pour `POST` demandes) ou `Request.QueryString["name"]` (pour `GET` demandes). Bien entendu, *nom* est le nom de l’élément à obtenir.
> 
> Le nom de l’élément que vous souhaitez obtenir doit être unique au sein de la collection que vous utilisez. C’est pourquoi les `Request` objet fournit les sous-ensembles comme `Request.Form` et `Request.QueryString`. Supposons que votre page contient un élément de formulaire nommé `userName` et *également* contient un cookie nommé `userName`. Si vous obtenez `Request["userName"]`, que vous souhaitez la valeur du formulaire ou le cookie est ambigu. Toutefois, si vous obtenez `Request.Form["userName"]` ou `Request.Cookie["userName"]`, vous êtes en cours explicite sur la valeur à obtenir.
> 
> Il est conseillé d’être précis et utiliser le sous-ensemble de `Request` vous intéresse, comme `Request.Form` ou `Request.QueryString`. Pour les pages simples que vous créez dans ce didacticiel, il probablement ne réellement fait aucune différence. Toutefois, lorsque vous créez des pages plus complexes, à l’aide de la version explicite `Request.Form` ou `Request.QueryString` peut vous aider à éviter les problèmes qui peuvent survenir lorsque la page contient un formulaire (ou plusieurs formulaires), les cookies, les valeurs de chaîne de requête et ainsi de suite.


## <a name="creating-a-query-by-using-a-search-term"></a>Création d’une requête à l’aide d’un terme à rechercher

Maintenant que vous savez comment obtenir le terme de recherche entré par l’utilisateur, vous pouvez créer une requête qui l’utilise. N’oubliez pas que pour obtenir tous les éléments de film hors de la base de données, vous utilisez une requête SQL qui ressemble à celle-ci :

`SELECT * FROM Movies`

Pour obtenir uniquement certains films, vous devez utiliser une requête qui inclut un `Where` clause. Cette clause permet de définir une condition sur laquelle les lignes sont retournées par la requête. Voici un exemple :

`SELECT * FROM Movies WHERE Genre = 'Action'`

Le format de base est `WHERE column = value`. Vous pouvez utiliser différents opérateurs outre juste `=`, comme `>` (supérieur à), `<` (inférieur à), `<>` (différent de), `<=` (inférieur ou égal à), etc., selon ce que vous cherchez.

Au cas où vous vous demandez, les instructions SQL ne sont pas respecter la casse &mdash; `SELECT` est identique à `Select` (ou même `select`). Toutefois, personnes souvent majuscule mots clés dans une instruction SQL, comme `SELECT` et `WHERE`, pour le rendre plus facile à lire.

### <a name="passing-the-search-term-as-a-parameter"></a>En passant le terme de recherche en tant que paramètre

Si vous recherchez un genre spécifique est relativement simple (`WHERE Genre = 'Action'`), mais que vous souhaitez être en mesure de rechercher n’importe quel genre entré par l’utilisateur. Pour ce faire, vous créez en tant que requête SQL qui inclut un espace réservé pour la valeur à rechercher. Il doit ressembler à cette commande :

`SELECT * FROM Movies WHERE Genre = @0`

L’espace réservé est la `@` caractères suivis de zéro. En effet, une requête peut contenir plusieurs espaces réservés et ils sont nommés `@0`, `@1`, `@2`, etc.

Pour définir la requête et en fait passer la valeur, vous utilisez le code comme suit :

[!code-sql[Main](form-basics/samples/sample4.sql)]

Ce code est similaire à ce que vous avez déjà fait pour afficher des données dans la grille. Les seules différences sont :

- La requête contient un espace réservé (`WHERE Genre = @0"`).
- La requête est placée dans une variable (`selectCommand`) ; avant que vous avez passé la requête directement à la `db.Query` (méthode).
- Lorsque vous appelez le `db.Query` méthode, vous passez la requête et la valeur à utiliser pour l’espace réservé. (Si la requête a dû à plusieurs espaces réservés, vous devez les passer tout comme des valeurs différentes à la méthode.)

Si vous placez tous ces éléments ensemble, vous obtenez le code suivant :

[!code-csharp[Main](form-basics/samples/sample5.cs)]

> [!NOTE] 
> 
> **Important !** À l’aide des espaces réservés (comme `@0`) pour passer des valeurs à une commande SQL est *extrêmement important* pour la sécurité. La façon que vous le voyez ici, avec des espaces réservés pour les données de variable, est la seule façon de créer des commandes SQL.
> 
> Impossible de créer une instruction SQL à assembler le texte littéral (concaténation) et les valeurs que vous obtenez à partir de l’utilisateur. Concaténation de l’entrée d’utilisateur dans une instruction SQL s’ouvre votre site vers un *attaque par injection SQL* où un utilisateur malveillant soumet les valeurs à votre page hack votre base de données. (Vous pouvez en savoir plus dans l’article [Injection SQL](https://msdn.microsoft.com/library/ms161953.aspx) le site Web MSDN.)


## <a name="updating-the-movies-page-with-search-code"></a>Mise à jour de la Page de films avec Code de recherche

Maintenant vous pouvez mettre à jour le code dans le *Movies.cshtml* fichier. Pour commencer, remplacez le code dans le bloc de code en haut de la page avec ce code :

[!code-csharp[Main](form-basics/samples/sample6.cs)]

La différence est que vous avez créé la requête dans le `selectCommand` variable, vous allez passer à `db.Query` plus tard. Placement de l’instruction SQL dans une variable vous permet de modifier l’instruction, ce que vous allez faire pour effectuer la recherche.

Vous avez également supprimé ces deux lignes, vous devez remettre en plus tard :

[!code-csharp[Main](form-basics/samples/sample7.cs)]

Vous ne souhaitez pas encore exécuté la requête (autrement dit, appeler `db.Query`) et vous ne souhaitez pas initialiser le `WebGrid` helper encore soit. Vous effectuerez ces éléments une fois que vous avez compris comment fonctionne l’instruction SQL doit s’exécuter.

Après ce bloc réécrit, vous pouvez ajouter la nouvelle logique pour la gestion de la recherche. Le code terminé ressemble à ce qui suit. Mettre à jour le code de votre page afin qu’il corresponde à cet exemple :

[!code-cshtml[Main](form-basics/samples/sample8.cshtml)]

La page fonctionne désormais comme suit. Chaque fois que la page s’exécute, le code s’ouvre à la base de données et la `selectCommand` variable est définie à l’instruction SQL qui obtient tous les enregistrements à partir de la `Movies` table. Le code initialise également le `searchTerm` variable.

Toutefois, si la demande en cours inclut une valeur pour le `searchGenre` élément, le code définit `selectCommand` à une autre requête, à savoir, à qui inclut le `Where` clause pour rechercher un genre. Il définit également `searchTerm` à tout ce qui a été transmis pour la zone de recherche (qui peut avoir la valeur nothing).

Quelle que soit l’élément SQL instruction se trouve dans `selectCommand`, le code appelle ensuite `db.Query` pour exécuter la requête, en lui passant tout est en `searchTerm`. Si vous ne contient aucun `searchTerm`, est sans importance, car dans ce cas il n’existe aucun paramètre à passer la valeur à `selectCommand` quand même.

Enfin, le code initialise le `WebGrid` application d’assistance à l’aide des résultats de la requête, tout comme avant.

Vous pouvez voir qu’en plaçant l’instruction SQL et le terme de recherche dans des variables, vous avez ajouté une grande souplesse pour le code. Comme vous le verrez plus loin dans ce didacticiel, vous pouvez utiliser cette structure de base et ajoutez une logique pour différents types de recherche.

## <a name="testing-the-search-by-genre-feature"></a>La fonctionnalité de recherche-par-Genre de test

Dans WebMatrix, exécutez le *Movies.cshtml* page. Vous consultez la page de la zone de texte pour le genre.

Entrez un genre que vous avez entré pour l’une de vos enregistrements de test, puis cliquez sur **recherche**. Cette fois vous consultez la liste des simplement les films qui correspondent à ce genre :

![Page de films après la recherche de genre 'Comedies'](form-basics/_static/image4.png)

Entrez un autre genre et relancer la recherche. Essayez d’entrer le genre à l’aide de toutes les lettres minuscules ou majuscules afin que vous pouvez voir que la recherche n’est pas la casse.

## <a name="remembering-what-the-user-entered"></a>« Mémorisation » de l’entrée utilisateur

Vous avez peut-être remarqué qu’une fois que vous avez entré un genre et cliqué sur **recherche Genre**, vous avez vu une liste de ce genre. Toutefois, la zone de texte de recherche était vide &mdash; en d’autres termes, il n’a pas été n’oubliez pas que vous avez entré.

Il est important de comprendre la raison pour laquelle ce problème se produit. Quand vous envoyez une page, le navigateur envoie une demande au serveur web. Lorsque ASP.NET reçoit la demande, il crée une nouvelle instance de la page, exécute le code qu’il contient et restitue ensuite de nouveau la page dans le navigateur. En effet, cependant, la page ne sait pas que vous travailliez avec une version précédente de lui-même. Les qu'il sait est qu’il a reçu une demande ayant certaines données de formulaire qu’il contient.

Chaque fois que vous demandez une page &mdash; pour la première fois ou en le soumettant &mdash; vous obtenez une nouvelle page. Le serveur web n’a aucun mémoire de votre dernière demande. Pas plus qu’ASP.NET, et n’effectue le navigateur. La seule connexion entre ces instances distinctes de la page est toutes les données que vous transmettez entre eux. Par exemple, si vous envoyez une page, la nouvelle instance de la page permettre obtenir les données de formulaire qui a été envoyées par l’instance antérieure. (Une autre façon de passer des données entre les pages consiste à utiliser des cookies.)

Une façon formelle pour décrire cette situation consiste à dire que les pages web sont *sans état*. Serveurs Web et les pages elles-mêmes et les éléments de la page ne gèrent pas toutes les informations concernant l’état précédent d’une page. Le site web a été conçu de cette façon, car la gestion de l’état des requêtes individuelles serait rapidement épuiser les ressources de serveurs web, souvent peut-être de gérer des milliers, voire des centaines de milliers de demandes par seconde.

C’est pourquoi la zone de texte est vide. Une fois que vous avez envoyé la page, ASP.NET créé une nouvelle instance de la page et exécuté par le code et le balisage. Lors de rien que du code qui dit ASP.NET pour placer une valeur dans la zone de texte. ASP.NET n’a pas été faire quoi que ce soit, et la zone de texte a été restituée sans une valeur qu’elle contient.

Il est en fait un moyen simple pour contourner ce problème. Le genre que vous avez entré dans la zone de texte *est* disponibles dans le code &mdash; dans `Request.QueryString["searchGenre"]`.

Mettre à jour le balisage de la zone de texte afin que le `value` attribut obtient sa valeur de `searchTerm`, telles que cet exemple :

[!code-html[Main](form-basics/samples/sample9.html?highlight=1)]

Dans cette page, vous pouvez aussi définir également la `value` attribut le `searchTerm` variable, étant donné que cette variable contient également le genre vous avez entré. Mais à l’aide du `Request` objet pour définir la `value` d’attributs comme indiqué ici est le moyen standard pour accomplir cette tâche. (En supposant vous souhaitez même cela &mdash; dans certaines situations, vous souhaiterez restituer la page *sans* valeurs dans les champs. Tout dépend de ce qui se passe votre application.)

> [!NOTE]
> Vous ne pouvez pas « se souviennent » la valeur d’une zone de texte qui est utilisée pour les mots de passe. Il serait une faille de sécurité pour permettre aux utilisateurs de remplir un champ de mot de passe à l’aide de code.


Réexécutez la page, entrez un genre, puis cliquez sur **recherche Genre**. Ce temps mais pas uniquement voir les résultats de la recherche, mais souvient de la zone de texte que vous avez entrés la dernière fois :

![Page indiquant que la zone de texte a 'mémorisées' l’entrée précédente](form-basics/_static/image5.png)

## <a name="searching-for-any-word-in-the-title"></a>Recherche de n’importe quel mot du titre

Vous pouvez maintenant rechercher n’importe quel genre, mais vous pouvez souhaiter également rechercher un titre. Il est difficile d’obtenir un titre exactement droit lorsque vous recherchez, au lieu de cela que vous pouvez rechercher un mot qui apparaît n’importe où à l’intérieur d’un titre. Pour ce faire dans SQL, vous utilisez la `LIKE` opérateur et la syntaxe comme suit :

`SELECT * FROM Movies WHERE Title LIKE '%adventure%'`

Cette commande Obtient toutes les vidéos dont les noms contiennent « adventure ». Lorsque vous utilisez la `LIKE` (opérateur), vous incluez le caractère générique `%` en tant que partie du terme de recherche. La recherche `LIKE 'adventure%'` signifie « commençant par « adventure » ». (Techniquement, cela signifie que « La chaîne « adventure » suivi ».) De même, le terme de recherche `LIKE '%adventure'` signifie « tout élément suivie de la chaîne « adventure », » qui est une autre façon de dire « terminant par « adventure » ».

Le terme de recherche `LIKE '%adventure%'` signifie par conséquent « avec « adventure » n’importe où dans le titre. » (Techniquement, « n’importe où dans le titre, suivi de « adventure », suivi par aucun élément. »)

À l’intérieur de la `<form>` élément, ajoutez le balisage suivant sous la clôture `</div>` balise pour la recherche de genre (juste avant la fermeture `</form>` élément) :

[!code-html[Main](form-basics/samples/sample10.html)]

Le code pour gérer cette recherche est semblable au code pour la recherche de genre, à ceci près que vous ayez à assembler le `LIKE` recherche. Dans le bloc de code en haut de la page, ajoutez ceci `if` bloquer juste après le `if` bloc pour la recherche de genre :

[!code-csharp[Main](form-basics/samples/sample11.cs)]

Ce code utilise la même logique que vous avez vu précédemment, sauf que la recherche utilise un `LIKE` opérateur et la place de code «`%`» avant et après le terme de recherche.

Notez comment elle a été facile d’ajouter une autre recherche à la page. Vous n’aviez faire s’est produite :

- Créer un `if` bloc testé pour voir si la zone de recherche pertinents a une valeur.
- Définir le `selectCommand` variable à une nouvelle instruction SQL.
- Définir le `searchTerm` variable à la valeur à passer à la requête.

Voici le bloc de code complet, qui contient la nouvelle logique pour une recherche de titre :

[!code-cshtml[Main](form-basics/samples/sample12.cshtml)]

Voici un résumé de ce que fait ce code :

- Les variables `searchTerm` et `selectCommand` sont initialisés en haut. Vous allez définir ces variables au terme de recherche approprié (le cas échéant) et la commande SQL appropriée en fonction de ce que fait par l’utilisateur dans la page. La recherche par défaut est le cas le plus simple d’obtenir tous les films à partir de la base de données.
- Dans les tests pour `searchGenre` et `searchTitle`, le code définit `searchTerm` à la valeur que vous souhaitez rechercher. Ces blocs de code est également défini `selectCommand` à une commande SQL appropriée pour cette recherche.
- Le `db.Query` méthode est appelée une seule fois, à l’aide de toute autre commande SQL est en `selectedCommand` et quelle que soit la valeur se trouve dans `searchTerm`. S’il n’existe aucun critère de recherche (aucun genre et aucun mot de titre), la valeur de `searchTerm` est une chaîne vide. Toutefois, qui n’a d’importance, car dans ce cas la requête ne nécessite pas un paramètre.

## <a name="testing-the-title-search-feature"></a>Test de la fonctionnalité de recherche de titre

Vous pouvez maintenant tester votre page de recherche terminée. Exécutez *Movies.cshtml*.

Entrez un genre et cliquez sur **recherche Genre**. La grille affiche les films de ce genre, comme avant.

Entrez un mot du titre, puis cliquez sur **recherche titre**. La grille affiche les films qui ont ce mot dans le titre.

![Page de films après recherche « Le » dans le titre](form-basics/_static/image6.png)

Renseignez les zones de texte, puis cliquez sur des boutons. La grille affiche toutes les films.

## <a name="combining-the-queries"></a>Combinaison de requêtes

Vous pouvez remarquer que les recherches que vous pouvez effectuer sont exclusifs. Vous ne pouvez pas rechercher le titre et le genre en même temps, même si les deux zones de recherche contiennent des valeurs. Par exemple, vous ne peut pas rechercher toutes les vidéos d’action dont le titre contient « Adventure ». (La page est codé maintenant, si vous entrez des valeurs pour le genre et titre, la recherche de titre obtient priorité). Pour créer une recherche qui combine les conditions, vous devrez créer une requête SQL qui a une syntaxe similaire à la suivante :

`SELECT * FROM Movies WHERE Genre = @0 AND Title LIKE @1`

Et vous devez exécuter la requête en utilisant une instruction comme suit (parlant à peu près) :

`var selectedData = db.Query(selectCommand, searchGenre, searchTitle);`

Création d’une logique permettant de nombreuses permutations de critères de recherche peut obtenir un peu impliquée, comme vous pouvez le voir. Par conséquent, nous allons vous arrêter là.

## <a name="coming-up-next"></a>Prochaine

Dans l’étape suivante du didacticiel, vous allez créer une page qui utilise un formulaire pour permettre aux utilisateurs d’ajouter des films à la base de données.

## <a name="complete-listing-for-movie-page-updated-with-search"></a>Liste complète pour la Page de film (mis à jour avec recherche)

[!code-cshtml[Main](form-basics/samples/sample13.cshtml)]

## <a name="additional-resources"></a>Ressources supplémentaires

- [Introduction à la programmation Web ASP.NET à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=202890)
- [Une Clause SQL WHERE](http://www.w3schools.com/sql/sql_where.asp) sur le site W3Schools
- [Définitions de méthode](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) article sur le site W3C

>[!div class="step-by-step"]
[Précédent](displaying-data.md)
[Suivant](entering-data.md)
