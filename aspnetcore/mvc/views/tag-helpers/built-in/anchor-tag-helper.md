---
title: "L’ancrage d’assistance de balise | Documents Microsoft"
author: pkellner
description: "Montre comment travailler avec l’application d’assistance de balise d’ancrage"
keywords: ASP.NET Core,tag helper
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: c045d485-d1dc-4cea-a675-46be83b7a011
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: e3754c4313f01bc746ccb8efe11611ae213e3955
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2017
---
# <a name="anchor-tag-helper"></a>Application d’assistance de balise d’ancrage

Par [Peter Kellner](http://peterkellner.net) 

L’application d’assistance de balise d’ancrage améliore le point d’ancrage HTML (`<a ... ></a>`) balise en ajoutant de nouveaux attributs. Le lien généré (sur le `href` balise) est créé en utilisant les nouveaux attributs. Cette URL peut inclure un protocole facultatifs, tel que https.

Le contrôleur de haut-parleur ci-dessous est utilisé dans les exemples dans ce document.

<br/>
**SpeakerController.cs** 

[!code-csharp[SpeakerController](sample/TagHelpersBuiltInAspNetCore/src/TagHelpersBuiltInAspNetCore/Controllers/SpeakerController.cs)]


## <a name="anchor-tag-helper-attributes"></a>Attributs d’assistance des balises d’ancrage

- - -

### <a name="asp-controller"></a>contrôleur d’ASP

`asp-controller`est utilisé pour associer le contrôleur qui doit servir à générer l’URL. Les contrôleurs spécifiés doivent exister dans le projet actuel. Le code suivant répertorie tous les haut-parleurs : 

```cshtml
<a asp-controller="Speaker" asp-action="Index">All Speakers</a>
```

Le balisage généré sera :

```html
<a href="/Speaker">All Speakers</a>
```

Si le `asp-controller` est spécifié et `asp-action` n’est pas le cas, la valeur par défaut `asp-action` correspondra à la méthode de contrôleur par défaut de la vue en cours d’exécution. Qu’est, dans l’exemple ci-dessus, si `asp-action` est omis, et ce programme d’assistance de balise d’ancrage est généré à partir de *HomeController*de `Index` vue (**/Home**), le balisage généré sera :


```html
<a href="/Home">All Speakers</a>
```

- - -
  
### <a name="asp-action"></a>action d’ASP

`asp-action`nom de la méthode d’action dans le contrôleur qui est inclus dans le texte généré `href`. Par exemple, le code suivant défini généré `href` pour pointer vers la page de détails du présentateur :

```html
<a asp-controller="Speaker" asp-action="Detail">Speaker Detail</a>
```

Le balisage généré sera :

```html
<a href="/Speaker/Detail">Speaker Detail</a>
```

Si aucun `asp-controller` attribut est spécifié, le contrôleur par défaut de l’appel de la vue de l’exécution de l’affichage actuel sera utilisé.  
 
Si l’attribut `asp-action` est `Index`, aucune action n’est ajoutée à l’URL menant à la valeur par défaut `Index` méthode appelée. L’action spécifiée (ou par défaut), doivent exister dans le contrôleur référencé dans `asp-controller`.

- - -
  
<a name="route"></a>
### <a name="asp-route-value"></a>ASP - route-{value}

`asp-route-`est un préfixe d’itinéraire avec des caractères génériques. Toute valeur que vous placez une fois que le tiret fin sera interprété comme un paramètre d’itinéraire potentiels. Si un itinéraire par défaut n’est trouvé, ce préfixe d’itinéraire est adjointe à href généré comme une valeur et le paramètre de demande. Dans le cas contraire, elle sera remplacée dans le modèle d’itinéraire.

En supposant que vous avez une méthode de contrôleur défini comme suit :

```csharp
public IActionResult AnchorTagHelper(string id)
{
    var speaker = new SpeakerData()
    {
        SpeakerId = 12
    };      
    return View(viewName, speaker);
}
```

Et le modèle d’itinéraire par défaut défini dans votre *Startup.cs* comme suit :

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
});

```

Le **cshtml** fichier qui contient l’application auxiliaire de balise d’ancrage nécessaire d’utiliser le **haut-parleur** paramètre de modèle passé à partir du contrôleur à la vue est la suivante :

```cshtml
@model SpeakerData
<!DOCTYPE html>
<html><body>
<a asp-controller='Speaker' asp-action='Detail' asp-route-id=@Model.SpeakerId>SpeakerId: @Model.SpeakerId</a>
<body></html>
```

Le code HTML généré sera ensuite comme suit, car **id** a été trouvé dans l’itinéraire par défaut.

```html
<a href='/Speaker/Detail/12'>SpeakerId: 12</a>
```

Si le préfixe d’itinéraire n’est pas partie du modèle de routage trouvé, qui est le cas avec les éléments suivants **cshtml** fichier :

```cshtml
@model SpeakerData
<!DOCTYPE html>
<html><body>
<a asp-controller='Speaker' asp-action='Detail' asp-route-speakerid=@Model.SpeakerId>SpeakerId: @Model.SpeakerId</a>
<body></html>
```

Le code HTML généré sera ensuite comme suit, car **speakerid** est introuvable dans l’itinéraire mis en correspondance :


```html
<a href='/Speaker/Detail?speakerid=12'>SpeakerId: 12</a>
```

Si le paramètre `asp-controller` ou `asp-action` ne sont pas spécifiés, le même traitement par défaut est adopté est dans le `asp-route` attribut.

- - -

### <a name="asp-route"></a>itinéraire d’ASP

`asp-route`fournit un moyen de créer une URL qui accède directement à un itinéraire nommé. À l’aide des attributs de routage, un itinéraire peut être nommé comme indiqué dans le `SpeakerController` et utilisé dans son `Evaluations` (méthode).

`Name = "speakerevals"`Indique à l’application d’assistance de balise d’ancrage pour générer un itinéraire directement à cette méthode de contrôleur à l’aide de l’URL `/Speaker/Evaluations`. Si `asp-controller` ou `asp-action` est spécifié en plus de `asp-route`, l’itinéraire généré est peut-être pas ce que vous attendez. `asp-route`ne doit pas être utilisé avec un des attributs `asp-controller` ou `asp-action` afin d’éviter un conflit d’itinéraire.

- - -

### <a name="asp-all-route-data"></a>ASP-all-données d’itinéraire

`asp-all-route-data`permet de créer un dictionnaire de paires clé / valeur où la clé est le nom du paramètre et la valeur est la valeur associée à cette clé.

Comme l’exemple ci-dessous illustre, un dictionnaire inline est créé et les données sont transmises à la vue razor. En guise d’alternative, les données peuvent également être passées avec votre modèle.

```cshtml
@{
    var dict =
        new Dictionary<string, string>
        {
            {"speakerId", "11"},
            {"currentYear", "true"}
        };
}
<a asp-route="speakerevalscurrent" 
   asp-all-route-data="dict">SpeakerEvals</a>
```

Le code ci-dessus génère l’URL suivante : http://localhost/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true

Lorsque vous cliquez sur le lien, la méthode de contrôleur `EvaluationsCurrent` est appelée. Elle est appelée, car ce contrôleur possède deux paramètres de chaîne qui correspond à ce qui a été créé à partir de la `asp-all-route-data` dictionnaire.

Si toutes les clés correspondent dans le dictionnaire de paramètres d’itinéraire, ces valeurs seront remplacées dans l’itinéraire, selon le cas et les autres valeurs de mise en correspondance ne seront générées en tant que paramètres de la demande.

- - -

### <a name="asp-fragment"></a>fragment d’ASP

`asp-fragment`définit un fragment d’URL à ajouter à l’URL. L’application d’assistance de balise d’ancrage ajoutera le caractère dièse (#). Si vous créez une balise :

```cshtml
<a asp-action="Evaluations" asp-controller="Speaker"  
   asp-fragment="SpeakerEvaluations">About Speaker Evals</a>
```

L’URL générée sera : http://localhost/Speaker/Evaluations#SpeakerEvaluations

Balises de hachage sont utiles lors de la création des applications côté client. Elles peuvent servir à faciliter le marquage et recherche dans JavaScript, par exemple.

- - -

### <a name="asp-area"></a>zone d’ASP

`asp-area`définit le nom de zone ASP.NET Core utilise pour définir l’itinéraire approprié. Voici des exemples de la façon dont l’attribut de zone provoque un remappage d’itinéraires. Paramètre `asp-area` blogs préfixes le répertoire `Areas/Blogs` aux itinéraires contrôleurs associées et les vues de cette balise d’ancrage.

* Nom du projet

  * *wwwroot*

  * *Zones*

    * *Blogs*

      * *Contrôleurs*

        * *HomeController.cs*

      * *Vues*

        * *Accueil*

          * *Index.cshtml*
          
          * *AboutBlog.cshtml*
          
  * *Contrôleurs*
  

        
Spécification d’une balise de zone qui est valide, comme ```area="Blogs"``` lorsque vous référencez le ```AboutBlog.cshtml``` fichier ressemble à ce qui suit à l’aide de l’application d’assistance de balise d’ancrage.

```cshtml
<a asp-action="AboutBlog" asp-controller="Home" asp-area="Blogs">Blogs About</a>
```

Le code HTML généré inclut le segment de zones et est comme suit :

```html
<a href="/Blogs/Home/AboutBlog">Blogs About</a>
```

> [!TIP]
> Pour les zones MVC travailler dans une application web, le modèle d’itinéraire doit inclure une référence à la zone si elle existe. Ce modèle, qui est le deuxième paramètre de la `routes.MapRoute` l’appel de méthode, apparaît sous la forme :`template: '"{area:exists}/{controller=Home}/{action=Index}"'`

- - -

### <a name="asp-protocol"></a>protocole d’ASP

Le `asp-protocol` de spécifier un protocole (tel que `https`) dans l’URL. Un exemple d’assistance à la balise d’ancrage qui inclut le protocole se présentera comme suit :

```<a asp-protocol="https" asp-action="About" asp-controller="Home">About</a>```

et générera du code HTML comme suit :

```<a href="https://localhost/Home/About">About</a>```

Le domaine dans l’exemple est localhost, mais l’application d’assistance de balise d’ancrage utilise de domaine du site Web public lors de la génération de l’URL.

- - -

## <a name="additional-resources"></a>Ressources supplémentaires

* [Zones](xref:mvc/controllers/areas)
