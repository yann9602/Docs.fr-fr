---
title: "Téléchargements de fichiers dans ASP.NET Core"
author: ardalis
description: "Comment utiliser la liaison de modèle et de diffusion en continu pour télécharger des fichiers dans ASP.NET MVC de base."
ms.author: riande
manager: wpickett
ms.date: 07/05/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/file-uploads
ms.openlocfilehash: bc1cfe0d6ee88a0af49cdff9ce77ad42f57b95f7
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="file-uploads-in-aspnet-core"></a>Téléchargements de fichiers dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

Actions d’ASP.NET MVC prend en charge le téléchargement d’un ou plusieurs fichiers à l’aide d’un modèle simple pour les fichiers plus petits de liaison ou de diffusion en continu pour les fichiers plus volumineux.

[Afficher ou télécharger l’exemple à partir de GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a>Téléchargement de petits fichiers avec la liaison de modèle

Pour télécharger des petits fichiers, vous pouvez utiliser un formulaire HTML plusieurs partie ou construire une demande POST à l’aide de JavaScript. Un exemple de formulaire à l’aide de Razor, qui prend en charge plusieurs fichiers téléchargés, est indiqué ci-dessous :

```html
<form method="post" enctype="multipart/form-data" asp-controller="UploadFiles" asp-action="Index">
    <div class="form-group">
        <div class="col-md-10">
            <p>Upload one or more files using this form:</p>
            <input type="file" name="files" multiple />
        </div>
    </div>
    <div class="form-group">
        <div class="col-md-10">
            <input type="submit" value="Upload" />
        </div>
    </div>
</form>
```

Pour prendre en charge les téléchargements de fichiers, les formulaires HTML doivent spécifier un `enctype` de `multipart/form-data`. Le `files` élément d’entrée ci-dessus prend en charge le téléchargement de plusieurs fichiers. Omettre la `multiple` attribut de cet élément d’entrée pour autoriser uniquement un seul fichier à télécharger. Le balisage ci-dessus est rendu dans un navigateur en tant que :

![Formulaire de téléchargement de fichier](file-uploads/_static/upload-form.png)

Les fichiers individuels téléchargés sur le serveur est accessible via [liaison de modèle](xref:mvc/models/model-binding) à l’aide de la [IFormFile](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.iformfile) interface. `IFormFile`a la structure suivante :

```csharp
public interface IFormFile
{
    string ContentType { get; }
    string ContentDisposition { get; }
    IHeaderDictionary Headers { get; }
    long Length { get; }
    string Name { get; }
    string FileName { get; }
    Stream OpenReadStream();
    void CopyTo(Stream target);
    Task CopyToAsync(Stream target, CancellationToken cancellationToken = null);
}
```

> [!WARNING]
> Ne s’appuient sur ou approuver le `FileName` propriété sans validation. Le `FileName` propriété doit uniquement être utilisée pour l’affichage.

Lors du téléchargement de fichiers à l’aide de la liaison de modèle et la `IFormFile` interface, la méthode d’action peut accepter un seul `IFormFile` ou `IEnumerable<IFormFile>` (ou `List<IFormFile>`) représentant plusieurs fichiers. L’exemple suivant effectue une itération sur un ou plusieurs des fichiers téléchargés, les enregistre dans le système de fichiers local et retourne le nombre total et la taille des fichiers téléchargés.

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

Les fichiers téléchargés à l’aide de la `IFormFile` technique sont mis en mémoire tampon en mémoire ou sur disque sur le serveur web avant d’être traité. À l’intérieur de la méthode d’action, le `IFormFile` contenu est accessible en tant que flux. Outre le système de fichiers local, des fichiers peuvent être diffusées à [le stockage Blob Azure](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs/) ou [Entity Framework](https://docs.microsoft.com/ef/core/index).

Pour stocker les données de fichier binaire dans une base de données à l’aide d’Entity Framework, définir une propriété de type `byte[]` sur l’entité :

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

Spécifiez une propriété viewmodel de type `IFormFile`:

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> `IFormFile`peut être utilisé directement comme un paramètre de méthode d’action ou une propriété viewmodel, comme indiqué ci-dessus.

Copie le `IFormFile` dans un flux et enregistrez-le dans le tableau d’octets :

```csharp
// POST: /Account/Register
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Register(RegisterViewModel model)
{
    ViewData["ReturnUrl"] = returnUrl;
    if  (ModelState.IsValid)
    {
        var user = new ApplicationUser {
          UserName = model.Email,
          Email = model.Email
        };
        using (var memoryStream = new MemoryStream())
        {
            await model.AvatarImage.CopyToAsync(memoryStream);
            user.AvatarImage = memoryStream.ToArray();
        }
    // additional logic omitted
    
    // Don't rely on or trust the model.AvatarImage.FileName property 
    // without validation.
}
```

> [!NOTE]
> Soyez prudent lorsque vous stockez des données binaires dans les bases de données relationnelles, comme il peut nuire aux performances.

## <a name="uploading-large-files-with-streaming"></a>Téléchargement de fichiers volumineux avec la diffusion en continu

Si la taille ou la fréquence des téléchargements de fichiers pose des problèmes de ressources de l’application, envisagez le téléchargement du fichier de diffusion en continu plutôt que de mise en mémoire tampon elle dans son intégralité, contrairement à l’approche de liaison de modèle ci-dessus. Lors de l’utilisation `IFormFile` et liaison de modèle est une solution beaucoup plus simple, diffusion en continu requiert plusieurs étapes pour implémenter correctement.

> [!NOTE]
> N’importe quel fichier mis en mémoire tampon unique supérieure à 64 Ko est déplacé de la mémoire vive dans un fichier temporaire sur le disque sur le serveur. Les ressources (disque RAM) utilisées par les téléchargements de fichiers varient selon le nombre et la taille des téléchargements de fichier simultanées. Diffusion en continu n’est pas tellement sur les performances, il est sur l’échelle. Si vous essayez de mettre en mémoire tampon trop de téléchargements, votre site se bloque lorsqu’il s’exécute en dehors de la mémoire ou l’espace disque.

L’exemple suivant illustre l’utilisation de JavaScript/angulaire à diffuser en continu à une action de contrôleur. Jeton de côté du fichier est généré à l’aide d’un attribut de filtre personnalisé et passées dans les en-têtes HTTP et non dans le corps de la demande. Étant donné que la méthode d’action traite les données transmises directement, liaison de modèle est désactivée par un autre filtre. Dans l’action, le contenu du formulaire est lus en utilisant un `MultipartReader`, qui lit chaque `MultipartSection`, du traitement du fichier ou enregistrer le contenu comme il convient. Une fois que toutes les sections ont été lus, l’action effectue ses propres liaison de modèle.

L’action initiale charge le formulaire et enregistre un jeton de côté dans un cookie (via la `GenerateAntiforgeryTokenCookieForAjax` attribut) :

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

L’attribut utilise les intégrée d’ASP.NET Core [Antiforgery](xref:security/anti-request-forgery) prise en charge d’un cookie avec un jeton de demande :

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

Angulaire transmet automatiquement un jeton de côté dans un en-tête de demande nommé `X-XSRF-TOKEN`. L’application ASP.NET MVC de base est configurée pour faire référence à cet en-tête dans sa configuration dans *Startup.cs*:

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

Le `DisableFormValueModelBinding` attribut, illustré ci-dessous, est utilisé pour désactiver la liaison de modèle pour le `Upload` méthode d’action.

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

Étant donné que la liaison de modèle est désactivée, le `Upload` méthode d’action n’accepte pas les paramètres. Il fonctionne directement avec le `Request` propriété du `ControllerBase`. A `MultipartReader` est utilisé pour lire chaque section. Le fichier est enregistré avec un nom de fichier GUID et les données de clé/valeur sont stockées dans un `KeyValueAccumulator`. Une fois que toutes les sections ont été lus, le contenu de la `KeyValueAccumulator` sont utilisés pour lier les données de formulaire à un type de modèle.

Le texte complet `Upload` méthode est indiquée ci-dessous :

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a>Résolution des problèmes

Voici certains problèmes courants rencontrés lors de l’utilisation avec le téléchargement de fichiers et leurs solutions possibles.

### <a name="unexpected-not-found-error-with-iis"></a>Erreur inattendue introuvable avec IIS

L’erreur suivante indique le chargement du fichier dépasse le serveur configurée `maxAllowedContentLength`:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Le paramètre par défaut est `30000000`, qui est d’environ 28,6 Mo. La valeur peut être personnalisée en modifiant *web.config*:

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- This will handle requests up to 50MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Ce paramètre s’applique uniquement à IIS. Le comportement ne se produit par défaut lors de l’hébergement sur Kestrel. Pour plus d’informations, consultez [limites des demandes \<requestLimits\>](https://docs.microsoft.com/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

### <a name="null-reference-exception-with-iformfile"></a>Exception de référence null avec IFormFile

Si votre contrôleur est acceptant téléchargé à l’aide de fichiers `IFormFile` , mais vous découvrirez que la valeur est toujours null, vérifiez la spécification de votre formulaire HTML un `enctype` valeur `multipart/form-data`. Si cet attribut n’est pas défini sur le `<form>` élément, le téléchargement du fichier ne se produit et n’importe quelle limite `IFormFile` arguments est null.
