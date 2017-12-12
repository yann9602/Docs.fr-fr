---
uid: mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view
title: "Examen des méthodes de modification et affichage Modifier | Documents Microsoft"
author: Rick-Anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/22/2015
ms.topic: article
ms.assetid: 52a4d5fe-aa31-4471-b3cb-a064f82cb791
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view
msc.type: authoredcontent
ms.openlocfilehash: 84aadccc18e7fa0fb56c7a78e144a1bf1038aac5
ms.sourcegitcommit: d1d8071d4093bf2444b5ae19d6e45c3d187e338b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2017
---
<a name="examining-the-edit-methods-and-edit-view"></a>Examen des méthodes de modification et la vue d’édition
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

[!INCLUDE[Tutorial Note](sample/code-location.md)]

Dans cette section, vous allez examiner générées `Edit` méthodes d’action et des vues pour le contrôleur vidéo. Mais tout d’abord prendra un détournement court faire plus de la date de publication. Ouvrez le *Models\Movie.cs* et ajoutez les lignes en surbrillance ci-dessous :

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample1.cs?highlight=2,12-14)]

Vous pouvez également rendre la culture de date spécifique, comme suit :

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample2.cs?highlight=3)]

Nous aborderons [DataAnnotations](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.aspx) dans le prochain didacticiel. L’attribut [Display](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.displayattribute.aspx) spécifie les éléments à afficher pour le nom d’un champ (dans le cas présent, « Release Date » au lieu de « ReleaseDate »). Le [DataType](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatypeattribute.aspx) attribut spécifie le type de données, dans ce cas il est une date, donc les informations stockées dans le champ d’heure ne sont pas affichées. Le [DisplayFormat](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.displayformatattribute.aspx) attribut est nécessaire pour un bogue dans le navigateur Chrome qui effectue le rendu des formats de date incorrecte.

Exécutez l’application et accédez à la `Movies` contrôleur. Maintenez le pointeur de la souris sur un **modifier** lien pour afficher l’URL à laquelle elle est liée.

![EditLink_sm](examining-the-edit-methods-and-edit-view/_static/image1.png)

Le **modifier** lien a été généré par le `Html.ActionLink` méthode dans le *Views\Movies\Index.cshtml* vue :

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample3.cshtml)]

![Html.ActionLink](examining-the-edit-methods-and-edit-view/_static/image2.png)

Le `Html` objet est un programme d’assistance qui est exposé à l’aide d’une propriété sur le [System.Web.Mvc.WebViewPage](https://msdn.microsoft.com/en-us/library/gg402107(VS.98).aspx) classe de base. Le `ActionLink` (méthode) de l’application d’assistance facilite la générer dynamiquement des liens hypertexte HTML qui renvoient à des méthodes d’action sur les contrôleurs. Le premier argument de la `ActionLink` méthode est le texte de lien à afficher (par exemple, `<a>Edit Me</a>`). Le deuxième argument est le nom de la méthode d’action à appeler (dans ce cas, le `Edit` action). L’argument final est un [objet anonyme](https://weblogs.asp.net/scottgu/archive/2007/05/15/new-orcas-language-feature-anonymous-types.aspx) qui génère les données d’itinéraire (dans ce cas, l’ID de 4).

Le lien généré est illustré dans l’image précédente est `http://localhost:1234/Movies/Edit/4`. L’itinéraire par défaut (établie dans *application\_Start\RouteConfig.cs*) utilise le modèle d’URL `{controller}/{action}/{id}`. Par conséquent, ASP.NET effectue une translation `http://localhost:1234/Movies/Edit/4` dans une demande pour le `Edit` méthode d’action de la `Movies` contrôleur avec le paramètre `ID` égal à 4. Examinez le code suivant à partir de la *application\_Start\RouteConfig.cs* fichier. Le [MapRoute](../../older-versions-1/controllers-and-routing/asp-net-mvc-routing-overview-cs.md) méthode est utilisée pour acheminer les requêtes HTTP à la méthode d’action et de contrôleur correcte et indiquez le paramètre d’ID facultatif. Le [MapRoute](../../older-versions-1/controllers-and-routing/asp-net-mvc-routing-overview-cs.md) méthode est également utilisée par le [HtmlHelpers](https://msdn.microsoft.com/en-us/library/system.web.mvc.htmlhelper(v=vs.108).aspx) comme `ActionLink` pour générer des URL étant donnés le contrôleur, méthode d’action et les données d’itinéraire.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample4.cs?highlight=7)]

Vous pouvez également passer des paramètres de méthode d’action à l’aide d’une chaîne de requête. Par exemple, l’URL `http://localhost:1234/Movies/Edit?ID=3` passe également le paramètre `ID` 3 pour le `Edit` méthode d’action de la `Movies` contrôleur.

![EditQueryString](examining-the-edit-methods-and-edit-view/_static/image3.png)

Ouvrez la `Movies` contrôleur. Les deux `Edit` méthodes d’action sont indiquées ci-dessous.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample5.cs?highlight=19-21)]

Notez que la deuxième méthode d’action `Edit` est précédée de l’attribut `HttpPost`. Cet attribut spécifie que la surcharge de la `Edit` méthode peut être appelée uniquement pour les demandes POST. Vous pouvez appliquer le `HttpGet` au premier attribut de modifier la méthode, mais qui n’est pas nécessaire car il s’agit de la valeur par défaut. (Nous allons faire référence aux méthodes d’action qui sont affectés de manière implicite le `HttpGet` sous la forme `HttpGet` méthodes.) Le [lier](https://msdn.microsoft.com/en-us/library/system.web.mvc.bindattribute(v=vs.108).aspx) attribut est un autre mécanisme de sécurité importantes qui empêche les pirates de publication remplacer des données à votre modèle. Vous devez uniquement inclure des propriétés dans l’attribut de liaison que vous souhaitez modifier. Vous pouvez lire sur overposting et l’attribut de liaison dans mon [sur-validation note de sécurité](https://go.microsoft.com/fwlink/?LinkId=317598). Dans le modèle simple utilisé dans ce didacticiel, nous sera liaison toutes les données dans le modèle. Le [ValidateAntiForgeryToken](https://msdn.microsoft.com/en-us/library/system.web.mvc.validateantiforgerytokenattribute(v=vs.108).aspx) attribut est utilisé pour lutter contre la contrefaçon d’une demande et qu’il est associé à `@Html.AntiForgeryToken()` dans le fichier de la vue edit (*Views\Movies\Edit.cshtml*), une partie est indiquée ci-dessous :

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample6.cshtml?highlight=9)]

`@Html.AntiForgeryToken()`génère un jeton anti-contrefaçon formulaire masqué qui doit correspondre dans le `Edit` méthode de la `Movies` contrôleur. Vous pouvez en savoir plus sur Cross-site demande falsification (également appelé XSRF ou CSRF) dans mon didacticiel [prévention XSRF/CSRF dans MVC](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md).

Le `HttpGet` `Edit` méthode accepte le paramètre d’ID de film, recherche le film à l’aide d’Entity Framework `Find` (méthode) et retourne le film sélectionné pour le mode édition. Si un film ne peut pas être trouvé, [HttpNotFound](https://msdn.microsoft.com/en-us/library/gg453938(VS.98).aspx) est retourné. Quand le système de génération de modèles automatique a créé la vue Edit, il a examiné la classe `Movie` et a créé le code pour restituer les éléments `<label>` et `<input>` de chaque propriété de la classe. L’exemple suivant montre le mode d’édition qui a été généré par le système de génération de modèles automatique de visual studio :

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample7.cshtml)]

Notez comment le modèle d’affichage a une `@model MvcMovie.Models.Movie` instruction en haut du fichier : Spécifie que la vue prévoit que le modèle pour le modèle d’affichage de type `Movie`.

Le code de modèle généré automatiquement utilise plusieurs *méthodes d’assistance* afin de rationaliser le balisage HTML. Le [ `Html.LabelFor` ](https://msdn.microsoft.com/en-us/library/gg401864(VS.98).aspx) affiche le nom du champ (&quot;titre&quot;, &quot;ReleaseDate&quot;, &quot;Genre&quot;, ou &quot;prix &quot;). Le [ `Html.EditorFor` ](https://msdn.microsoft.com/en-us/library/system.web.mvc.html.editorextensions.editorfor(VS.98).aspx) helper effectue le rendu HTML `<input>` élément. Le [ `Html.ValidationMessageFor` ](https://msdn.microsoft.com/en-us/library/system.web.mvc.html.validationextensions.validationmessagefor(VS.98).aspx) affiche les messages de validation associés à cette propriété.

Exécutez l’application et accédez à la */Movies* URL. Cliquez sur un lien **Edit**. Dans le navigateur, affichez la source de la page. Le code HTML pour l’élément de formulaire est présenté ci-dessous.

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample8.cshtml?highlight=1-2)]

Le `<input>` sont des éléments dans le code HTML `<form>` élément dont `action` attribut a la valeur à valider dans le *films/modifier* URL. Les données du formulaire sont publiées sur le serveur lors de la **enregistrer** bouton est activé. La deuxième ligne affiche le texte masqué [XSRF](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) jeton généré par le `@Html.AntiForgeryToken()` appeler.

## <a name="processing-the-post-request"></a>Traitement de la requête POST

Le code suivant montre la version `HttpPost` de la méthode d’action `Edit`.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample9.cs)]

Le [ValidateAntiForgeryToken](https://msdn.microsoft.com/en-us/library/system.web.mvc.validateantiforgerytokenattribute(v=vs.108).aspx) attribut valide le [XSRF](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) jeton généré par le `@Html.AntiForgeryToken()` appeler dans la vue.

Le [classeur de modèles ASP.NET MVC](https://msdn.microsoft.com/en-us/library/dd410405.aspx) prend les valeurs de formulaire publiées et crée un `Movie` objet passé en tant que le `movie` paramètre. La méthode `ModelState.IsValid` vérifie que les données envoyées dans le formulaire peuvent être utilisées pour changer (modifier ou mettre à jour) un objet `Movie`. Si les données sont valides, les données de film sont enregistrées dans le `Movies` collection de la `db(MovieDBContext` instance). Les nouvelles données de film sont enregistrées dans la base de données en appelant le `SaveChanges` méthode `MovieDBContext`. Après avoir enregistré les données, le code redirige l’utilisateur vers la méthode d’action `Index` de la classe `MoviesController`, qui affiche la collection de films, avec notamment les modifications qui viennent d’être apportées.

Dès que la validation côté client détermine que les valeurs d’un champ ne sont pas valides, un message d’erreur s’affiche. Si vous désactivez JavaScript, vous n’avez pas la validation côté client, mais le serveur détecte les valeurs publiées ne sont pas valides et les valeurs de formulaire seront affiche de nouveau avec les messages d’erreur. Plus loin dans ce didacticiel, nous examiner plus en détail la validation.

Le `Html.ValidationMessageFor` applications auxiliaires dans le *Edit.cshtml* vue de modèle, prenez soin d’afficher les messages d’erreur appropriés.

![abcNotValid](examining-the-edit-methods-and-edit-view/_static/image4.png)

Tous les `HttpGet` méthodes suivent un modèle similaire. Ils reçoivent un objet vidéo (ou une liste d’objets, dans le cas de `Index`) et passer le modèle à la vue. Le `Create` méthode passe un objet de séquence vide à la vue de créer. Toutes les méthodes qui créent, modifient, suppriment ou changent d’une quelconque manière des données le font dans la surcharge `HttpPost` de la méthode. Modification des données dans une méthode HTTP GET est un risque de sécurité, comme décrit dans le billet de blog [ASP.NET MVC Conseil #46 : n’utilisez pas supprimer les liens parce qu’elles créent des failles de sécurité](http://stephenwalther.com/blog/archive/2009/01/21/asp.net-mvc-tip-46-ndash-donrsquot-use-delete-links-because.aspx). Modification des données dans une méthode GET enfreint également les meilleures pratiques de HTTP et l’architecture [reste](http://en.wikipedia.org/wiki/Representational_State_Transfer) de modèle, qui spécifie que les demandes GET ne doivent pas modifier l’état de votre application. En d’autres termes, une opération GET doit être sûre, ne doit avoir aucun effet secondaire et ne doit pas modifier vos données persistantes.

Si vous utilisez un ordinateur en langue anglaise, vous pouvez ignorer cette section et passer à l’étape suivante du didacticiel. Vous pouvez télécharger la version Globalize de ce didacticiel [ici](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=16475). Pour un didacticiel de deux parties excellente sur l’internationalisation, consultez [ASP.NET MVC 5 internationalisation de Nadeem](http://afana.me/post/aspnet-mvc-internationalization.aspx).


> [!NOTE]
> pour prendre en charge la validation jQuery pour les paramètres régionaux non anglais qui utilisent une virgule (&quot;,&quot;) pour une virgule décimale et les formats de date non anglais des États-Unis, vous devez inclure *globalize.js* et vos  *cultures/globalize.cultures.js* fichier (à partir de [https://github.com/jquery/globalize](https://github.com/jquery/globalize) ) et le JavaScript pour utiliser `Globalize.parseFloat`. Vous pouvez obtenir la validation non anglaises de jQuery à partir de NuGet. (N’installez pas Globalize si vous utilisez des paramètres régionaux anglais.)


1. À partir de la **outils** menu, cliquez sur **NuGetLibrary Package Manager**, puis cliquez sur **gérer les Packages NuGet pour la Solution**.  
  
    ![](examining-the-edit-methods-and-edit-view/_static/image5.png)
2. Dans le volet gauche, sélectionnez  **Parcourir*.* ** (Voir l’image ci-dessous.)
3. Dans la zone d’entrée, entrez *Globalize**.  
  
    ![](examining-the-edit-methods-and-edit-view/_static/image6.png)Choisissez `jQuery.Validation.Globalize`, choisissez `MvcMovie` et cliquez sur **installer**. Le *Scripts\jquery.globalize\globalize.js* fichier sera ajouté à votre projet. Le *Scripts\jquery.globalize\cultures\* nombreux culture contiendra les fichiers JavaScript. Il peut prendre cinq minutes pour installer ce package.

 Le code suivant montre les modifications dans le fichier Views\Movies\Edit.cshtml : 

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample10.cshtml)]

Pour éviter de répéter ce code dans chaque vue Edition, vous pouvez la déplacer vers le fichier de disposition. Pour optimiser le téléchargement du script, consultez mon didacticiel [Bundling and Minification](../../performance/bundling-and-minification.md).

Pour plus d’informations, consultez [ASP.NET MVC 3 internationalisation](http://afana.me/post/aspnet-mvc-internationalization.aspx) et [internationalisation de 3 ASP.NET MVC - Part 2 (NerdDinner)](http://afana.me/post/aspnet-mvc-internationalization-part-2.aspx).

Comme solution temporaire, si vous ne parvenez validation travaillant dans vos paramètres régionaux, vous pouvez forcer l’ordinateur à utiliser anglais (États-Unis) ou vous pouvez désactiver JavaScript dans votre navigateur. Pour forcer votre ordinateur pour utiliser l’anglais, vous pouvez ajouter l’élément de globalisation à la racine de projets *web.config* fichier. Le code suivant illustre l’élément de globalisation avec la culture définie sur l’anglais des États-Unis.

[!code-xml[Main](examining-the-edit-methods-and-edit-view/samples/sample11.xml)]

<a id="gettingstarted"></a><a id="jQueryAjaxJSON"></a>Dans l’étape suivante du didacticiel, nous allons implémenter la fonctionnalité de recherche.

>[!div class="step-by-step"]
[Précédent](accessing-your-models-data-from-a-controller.md)
[Suivant](adding-search.md)
