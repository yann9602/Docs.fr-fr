---
title: "Mise en forme des données de réponse dans ASP.NET MVC de base"
author: ardalis
description: "Découvrez comment mettre en forme les données de réponse dans ASP.NET MVC de base."
keywords: "ASP.NET Core, les données de réponse, IOutputFormatter, IActionResult"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: c056df45-d013-4826-91a1-4a092bae1ea5
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/formatting
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7fd1ecb61c7b1559f8bd304c30f7201864bd448
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-formatting-response-data-in-aspnet-core-mvc"></a>Introduction à la mise en forme des données de réponse dans ASP.NET MVC de base

Par [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC prend en charge pour la mise en forme des données de réponse, au format fixe ou en réponse aux spécifications du client.

[Afficher ou télécharger l’exemple à partir de GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/formatting/sample).

## <a name="format-specific-action-results"></a>Résultats de l’Action de format spécifiques

Certains types de résultats d’action sont spécifiques à un format particulier, tel que `JsonResult` et `ContentResult`. Actions peuvent retourner des résultats spécifiques qui sont toujours mis en forme d’une manière particulière. Par exemple, retourner un `JsonResult` retourne des données au format JSON, indépendamment des préférences du client. De même, en retournant un `ContentResult` retournent des données de chaîne au format texte brut (comme sera simplement retourner une chaîne).

> [!NOTE]
> Une action n’est pas nécessaire pour retourner un type particulier ; MVC prend en charge la valeur de retour de n’importe quel objet. Si une action retourne un `IActionResult` mise en œuvre et le contrôleur hérite `Controller`, les développeurs ont de nombreuses méthodes d’assistance correspondant à la plupart des options. Résultats des actions qui retournent des objets qui ne sont pas `IActionResult` types seront sérialisées à l’aide de la commande appropriée `IOutputFormatter` implémentation.

Pour retourner des données dans un format spécifique à partir d’un contrôleur qui hérite de la `Controller` classe de base, utilisez la méthode d’assistance intégrée `Json` à retourner JSON et `Content` pour le texte brut. Votre méthode d’action doit retourner le type de résultat spécifique (par exemple, `JsonResult`) ou `IActionResult`.

Recherche de données au format JSON :

[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

Exemple de réponse à partir de cette action :

![Onglet réseau des outils de développement dans Microsoft Edge indiquant le type de contenu de la réponse est application/json](formatting/_static/json-response.png)

Notez que le type de contenu de la réponse est `application/json`, comme illustré dans la liste des demandes du réseau et dans la section en-têtes de réponse. Notez également la liste des options présentées par le navigateur (dans ce cas, Microsoft Edge) dans l’en-tête Accept dans la section en-têtes de la requête. La technique actuelle ignore cet en-tête ; Il s’est présenté ci-dessous.

Pour renvoyer les données mises en forme en texte brut, utilisez `ContentResult` et `Content` assistance :

[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

Une réponse à partir de cette action :

![Onglet réseau des outils de développement dans Microsoft Edge indiquant le type de contenu de la réponse est le texte brut](formatting/_static/text-response.png)

Notez dans ce cas le `Content-Type` retournée est `text/plain`. Vous pouvez également obtenir ce comportement à l’aide d’un type de réponse de chaîne :

[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> Pour les actions non trivial avec plusieurs options (par exemple, les codes d’état HTTP différents en fonction du résultat des opérations effectuées) ou des types de retour, préférez `IActionResult` comme type de retour.

## <a name="content-negotiation"></a>Négociation de contenu

Négociation de contenu (*conneg* en abrégé) se produit lorsque le client spécifie un [en-tête Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Le format par défaut utilisé par ASP.NET MVC de base est JSON. Négociation de contenu est implémentée par `ObjectResult`. Il est également créé dans le code d’état retournés de résultats d’action spécifique à partir des méthodes d’assistance (qui sont basées sur `ObjectResult`). Vous pouvez également retourner un type de modèle (une classe que vous avez défini comme type de transfert de données) et le framework encapsule automatiquement dans un `ObjectResult` pour vous.

La méthode d’action suivante utilise le `Ok` et `NotFound` méthodes d’assistance :

[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

Une réponse au format JSON s’affichera, sauf si un autre format a été demandé et le serveur peut retourner le format demandé. Vous pouvez utiliser un outil tel que [Fiddler](http://www.telerik.com/fiddler) pour créer une demande qui inclut un en-tête Accept et spécifiez un autre format. Dans ce cas, si le serveur possède un *formateur* qui peut produire une réponse dans le format demandé, le résultat est renvoyé dans le format préféré au client.

![Affichage créé manuellement dans la console Fiddler obtenir une demande avec une valeur d’en-tête Accept d’application/xml](formatting/_static/fiddler-composer.png)

Dans la capture d’écran ci-dessus, l’éditeur de Fiddler a été utilisé pour générer une demande, en spécifiant `Accept: application/xml`. Par défaut, ASP.NET MVC de base prend uniquement en charge JSON, donc, même si un autre format est spécifié, le résultat retourné est toujours au format JSON. Vous verrez comment ajouter des modules de formatage supplémentaires dans la section suivante.

Actions de contrôleur peuvent retourner POCOs (Plain Old CLR Objects), auquel cas ASP.NET MVC crée automatiquement un `ObjectResult` qui encapsule l’objet. Le client obtiendra l’objet sérialisé au format (format JSON est la valeur par défaut ; vous pouvez configurer XML ou autres formats). Si l’objet retourné est `null`, le framework retournera un `204 No Content` réponse.

Retourner un type d’objet :

[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

Dans l’exemple, une demande pour un alias valide auteur reçoit une réponse 200 OK avec des données de l’auteur. Une demande d’un alias non valide répondra 204 pas de contenu. Captures d’écran montrant la réponse dans les formats XML et JSON sont indiquées ci-dessous.

### <a name="content-negotiation-process"></a>Processus de négociation de contenu

Contenu *négociation* intervient uniquement si une `Accept` en-tête s’affiche dans la demande. Lorsqu’une demande contient un en-tête accept, le framework énumère les types de médias dans l’en-tête accept par ordre de préférence et tente de trouver un formateur capable de générer une réponse dans un des formats spécifiés par l’en-tête accept. Si aucun formateur n’est trouvée qui peut satisfaire la demande du client, le framework va tenter de trouver le premier formateur capable de générer une réponse (à moins que le développeur a configuré l’option sur `MvcOptions` pour retourner 406 non Acceptable à la place). Si la demande spécifie XML, mais le formateur XML n’a pas été configuré, le module de formatage JSON être utilisé. En règle générale, si aucun formateur n’est configuré, qui peut fournir le format demandé, puis le premier formateur que vous pouvez mettre en forme l’objet est utilisé. Si aucun en-tête n’est spécifié, le premier formateur capable de gérer l’objet doit être retournée sera utilisé pour sérialiser la réponse. Dans ce cas, il n’existe pas de n’importe quel lieu de négociation - le serveur consiste à déterminer quel format, il utilisera.

> [!NOTE]
> Si l’en-tête Accept contient `*/*`, l’en-tête sera ignoré, sauf si `RespectBrowserAcceptHeader` a la valeur true sur `MvcOptions`.

### <a name="browsers-and-content-negotiation"></a>Navigateurs et la négociation de contenu

Contrairement aux clients d’API par défaut, les navigateurs web ont tendance à fournir `Accept` en-têtes qui incluent un large éventail de formats, y compris les caractères génériques. Par défaut, lorsque le framework détecte que la demande provient d’un navigateur, il ignore le `Accept` en-tête et le retour à la place le contenu de l’application configurée format par défaut (JSON, sauf si configuré dans le cas contraire). Cela fournit une expérience plus cohérente lors de l’utilisation de différents navigateurs pour consommer des API.

Si vous préférez en-têtes accept de votre navigateur de honorer l’application, vous pouvez le configurer en tant que partie de la configuration de MVC en définissant `RespectBrowserAcceptHeader` à `true` dans les `ConfigureServices` méthode dans *Startup.cs*.

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
}
```

## <a name="configuring-formatters"></a>Formateurs de configuration

Si votre application doit prendre en charge des formats supplémentaires au-delà de la valeur par défaut de JSON, vous pouvez ajouter des packages NuGet et configurer MVC pour les prendre en charge. Il existe des formateurs distincts pour l’entrée et de sortie. Les formateurs d’entrée sont utilisés par [liaison de modèle](model-binding.md); les formateurs de sortie sont utilisés pour le formatage de réponses. Vous pouvez également configurer [personnalisé formateurs](../advanced/custom-formatters.md).

### <a name="adding-xml-format-support"></a>Ajout de prise en charge du Format XML

Pour ajouter la prise en charge de la mise en forme XML, vous devez installer le `Microsoft.AspNetCore.Mvc.Formatters.Xml` package NuGet.

Ajouter le XmlSerializerFormatters à la configuration de MVC à *Startup.cs*:

[!code-csharp[Main](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

Ou bien, vous pouvez ajouter simplement le formateur de sortie :

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlSerializerOutputFormatter());
});
```

Ces deux approches sérialise les résultats à l’aide de `System.Xml.Serialization.XmlSerializer`. Si vous préférez, vous pouvez utiliser la `System.Runtime.Serialization.DataContractSerializer` en ajoutant le formateur associé :

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlDataContractSerializerOutputFormatter());
});
```

Une fois que vous avez ajouté la prise en charge de la mise en forme XML, vos méthodes de contrôleur doivent retourner le format approprié en fonction de la demande `Accept` en-tête, comme cette Fiddler montre :

![Console de Fiddler : brut de l’onglet de la requête affiche la valeur d’en-tête Accept est application/xml. L’onglet brut pour la réponse indique la valeur d’en-tête Content-Type de l’application/xml.](formatting/_static/xml-response.png)

Vous pouvez voir dans l’onglet inspecteurs qui la demande GET brute a été effectuée avec un `Accept: application/xml` ensemble d’en-têtes. Le volet de réponse affiche le `Content-Type: application/xml` en-tête et le `Author` objet a été sérialisé en XML.

Utilisez l’onglet pour modifier la requête pour spécifier `application/json` dans le `Accept` en-tête. Exécutez la requête et la réponse est au format JSON :

![Console de Fiddler : brut de l’onglet de la requête affiche la valeur d’en-tête Accept est application/json. L’onglet brut pour la réponse indique la valeur d’en-tête Content-Type de l’application/json.](formatting/_static/json-response-fiddler.png)

Dans cette capture d’écran, vous pouvez voir la demande définit un en-tête de `Accept: application/json` et la réponse indique que le même que son `Content-Type`. Le `Author` objet est affiché dans le corps de la réponse, au format JSON.

### <a name="forcing-a-particular-format"></a>Forcer un Format particulier

Si vous souhaitez limiter les formats de réponse pour une action spécifique que vous pouvez, vous pouvez appliquer la `[Produces]` filtre. Le `[Produces]` filtre spécifie les formats de réponse pour une action spécifique (ou un contrôleur). Comme la plupart [filtres](../controllers/filters.md), cela peut être appliqué à l’action, le contrôleur ou la portée globale.

```csharp
[Produces("application/json")]
public class AuthorsController
```

Le `[Produces]` filtre force toutes les actions au sein de la `AuthorsController` pour renvoyer des réponses au format JSON, même si d’autres modules de formatage a été configurés pour l’application et le client fourni un `Accept` en-tête de demande d’une autre disponibles format. Consultez [filtres](../controllers/filters.md) pour en savoir plus, y compris comment appliquer des filtres globaux.

### <a name="special-case-formatters"></a>Formateurs de cas spéciaux

Certains cas sont implémentées à l’aide de formateurs intégrés. Par défaut, `string` seront formatées en tant que types de retour *texte/brut* (*texte/html* si demandé `Accept` en-tête). Ce comportement peut être supprimé en supprimant le `TextOutputFormatter`. Supprimer des formateurs dans le `Configure` méthode dans *Startup.cs* (voir ci-dessous). Les actions qui ont un objet de modèle retournent type ne retournera un 204 aucun réponse contenu lors du retour `null`. Ce comportement peut être supprimé en supprimant le `HttpNoContentOutputFormatter`. Le code suivant supprime le `TextOutputFormatter` et `HttpNoContentOutputFormatter`.

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

Sans le `TextOutputFormatter`, `string` retournent des types de retour 406 non Acceptable, par exemple. Notez que si un formateur XML existe, elle mettra en forme `string` types de retour si la `TextOutputFormatter` est supprimé.

Sans le `HttpNoContentOutputFormatter`, objets null sont mis en forme à l’aide du formateur configuré. Par exemple, le module de formatage JSON retourne simplement une réponse avec un corps de `null`, tandis que le formateur XML retourne un élément XML vide avec l’attribut `xsi:nil="true"` défini.

## <a name="response-format-url-mappings"></a>Mappages d’URL de Format de réponse

Les clients peuvent demander un format particulier dans le cadre de l’URL, comme dans la chaîne de requête ou une partie du chemin d’accès, ou à l’aide d’une extension de fichier de format spécifiques telles que .xml ou .json. Le mappage de chemin d’accès de la demande doit être spécifié dans l’itinéraire à l’aide de l’API. Exemple :

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

Cet itinéraire permettrait le format demandé en tant qu’une extension de fichier facultatif. Le `[FormatFilter]` attribut vérifie l’existence de la valeur de format dans le `RouteData` et mappe le format de réponse au formateur approprié lors de la création de la réponse.

| Itinéraire                      | Formateur                          |
| -------------------------- | ---------------------------------- |
| `/products/GetById/5`      | Le formateur de sortie par défaut       |
| `/products/GetById/5.json` | Le module de formatage JSON (si configuré) |
| `/products/GetById/5.xml`  | Le formateur XML (si configuré)  |
