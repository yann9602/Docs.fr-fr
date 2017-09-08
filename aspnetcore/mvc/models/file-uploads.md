---
title: "Téléchargements de fichiers dans ASP.NET Core"
author: ardalis
description: "Comment utiliser la liaison de modèle et de diffusion en continu pour télécharger des fichiers dans ASP.NET MVC de base."
keywords: "ASP.NET Core, le téléchargement du fichier de modèle de liaison, IFormFile, diffusion en continu"
ms.author: riande
manager: wpickett
ms.date: 7/5/2017
ms.topic: article
ms.assetid: ebc98159-a028-4a94-b06c-43981c79c6be
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/file-uploads
ms.openlocfilehash: 78cc9cd846f9b0963dbba9069c86ca295f7a32e4
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="file-uploads-in-aspnet-core"></a><span data-ttu-id="1d042-104">Téléchargements de fichiers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d042-104">File uploads in ASP.NET Core</span></span>

<span data-ttu-id="1d042-105">Par [Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="1d042-105">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="1d042-106">Actions d’ASP.NET MVC prend en charge le téléchargement d’un ou plusieurs fichiers à l’aide d’un modèle simple pour les fichiers plus petits de liaison ou de diffusion en continu pour les fichiers plus volumineux.</span><span class="sxs-lookup"><span data-stu-id="1d042-106">ASP.NET MVC actions support uploading of one or more files using simple model binding for smaller files or streaming for larger files.</span></span>

[<span data-ttu-id="1d042-107">Afficher ou télécharger l’exemple à partir de GitHub</span><span class="sxs-lookup"><span data-stu-id="1d042-107">View or download sample from GitHub</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a><span data-ttu-id="1d042-108">Téléchargement de petits fichiers avec la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="1d042-108">Uploading small files with model binding</span></span>

<span data-ttu-id="1d042-109">Pour télécharger des petits fichiers, vous pouvez utiliser un formulaire HTML plusieurs partie ou construire une demande POST à l’aide de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1d042-109">To upload small files, you can use a multi-part HTML form or construct a POST request using JavaScript.</span></span> <span data-ttu-id="1d042-110">Un exemple de formulaire à l’aide de Razor, qui prend en charge plusieurs fichiers téléchargés, est indiqué ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="1d042-110">An example form using Razor, which supports multiple uploaded files, is shown below:</span></span>

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

<span data-ttu-id="1d042-111">Pour prendre en charge les téléchargements de fichiers, les formulaires HTML doivent spécifier un `enctype` de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="1d042-111">In order to support file uploads, HTML forms must specify an `enctype` of `multipart/form-data`.</span></span> <span data-ttu-id="1d042-112">Le `files` élément d’entrée ci-dessus prend en charge le téléchargement de plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="1d042-112">The `files` input element shown above supports uploading multiple files.</span></span> <span data-ttu-id="1d042-113">Omettre la `multiple` attribut de cet élément d’entrée pour autoriser uniquement un seul fichier à télécharger.</span><span class="sxs-lookup"><span data-stu-id="1d042-113">Omit the `multiple` attribute on this input element to allow just a single file to be uploaded.</span></span> <span data-ttu-id="1d042-114">Le balisage ci-dessus est rendu dans un navigateur en tant que :</span><span class="sxs-lookup"><span data-stu-id="1d042-114">The above markup renders in a browser as:</span></span>

![Formulaire de téléchargement de fichier](file-uploads/_static/upload-form.png)

<span data-ttu-id="1d042-116">Les fichiers individuels téléchargés sur le serveur est accessible via [liaison de modèle](xref:mvc/models/model-binding) à l’aide de la [IFormFile](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.iformfile) interface.</span><span class="sxs-lookup"><span data-stu-id="1d042-116">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using the [IFormFile](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.iformfile) interface.</span></span> <span data-ttu-id="1d042-117">`IFormFile`a la structure suivante :</span><span class="sxs-lookup"><span data-stu-id="1d042-117">`IFormFile` has the following structure:</span></span>

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
> <span data-ttu-id="1d042-118">Ne s’appuient sur ou approuver le `FileName` propriété sans validation.</span><span class="sxs-lookup"><span data-stu-id="1d042-118">Don't rely on or trust the `FileName` property without validation.</span></span> <span data-ttu-id="1d042-119">Le `FileName` propriété doit uniquement être utilisée pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="1d042-119">The `FileName` property should only be used for display purposes.</span></span>

<span data-ttu-id="1d042-120">Lors du téléchargement de fichiers à l’aide de la liaison de modèle et la `IFormFile` interface, la méthode d’action peut accepter un seul `IFormFile` ou `IEnumerable<IFormFile>` (ou `List<IFormFile>`) représentant plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="1d042-120">When uploading files using model binding and the `IFormFile` interface, the action method can accept either a single `IFormFile` or an `IEnumerable<IFormFile>` (or `List<IFormFile>`) representing several files.</span></span> <span data-ttu-id="1d042-121">L’exemple suivant effectue une itération sur un ou plusieurs des fichiers téléchargés, les enregistre dans le système de fichiers local et retourne le nombre total et la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="1d042-121">The following example loops through one or more uploaded files, saves them to the local file system, and returns the total number and size of files uploaded.</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

<span data-ttu-id="1d042-122">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="1d042-122">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]</span></span>

<span data-ttu-id="1d042-123">Les fichiers téléchargés à l’aide de la `IFormFile` technique sont mis en mémoire tampon en mémoire ou sur disque sur le serveur web avant d’être traité.</span><span class="sxs-lookup"><span data-stu-id="1d042-123">Files uploaded using the `IFormFile` technique are buffered in memory or on disk on the web server before being processed.</span></span> <span data-ttu-id="1d042-124">À l’intérieur de la méthode d’action, le `IFormFile` contenu est accessible en tant que flux.</span><span class="sxs-lookup"><span data-stu-id="1d042-124">Inside the action method, the `IFormFile` contents are accessible as a stream.</span></span> <span data-ttu-id="1d042-125">Outre le système de fichiers local, des fichiers peuvent être diffusées à [le stockage Blob Azure](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs) ou [Entity Framework](https://docs.microsoft.com/ef/core/index).</span><span class="sxs-lookup"><span data-stu-id="1d042-125">In addition to the local file system, files can be streamed to [Azure Blob storage](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs) or [Entity Framework](https://docs.microsoft.com/ef/core/index).</span></span>

<span data-ttu-id="1d042-126">Pour stocker les données de fichier binaire dans une base de données à l’aide d’Entity Framework, définir une propriété de type `byte[]` sur l’entité :</span><span class="sxs-lookup"><span data-stu-id="1d042-126">To store binary file data in a database using Entity Framework, define a property of type `byte[]` on the entity:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

<span data-ttu-id="1d042-127">Spécifiez une propriété viewmodel de type `IFormFile`:</span><span class="sxs-lookup"><span data-stu-id="1d042-127">Specify a viewmodel property of type `IFormFile`:</span></span>

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="1d042-128">`IFormFile`peut être utilisé directement comme un paramètre de méthode d’action ou une propriété viewmodel, comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="1d042-128">`IFormFile` can be used directly as an action method parameter or as a viewmodel property, as shown above.</span></span>

<span data-ttu-id="1d042-129">Copie le `IFormFile` dans un flux et enregistrez-le dans le tableau d’octets :</span><span class="sxs-lookup"><span data-stu-id="1d042-129">Copy the `IFormFile` to a stream and save it to the byte array:</span></span>

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
> <span data-ttu-id="1d042-130">Soyez prudent lorsque vous stockez des données binaires dans les bases de données relationnelles, comme il peut nuire aux performances.</span><span class="sxs-lookup"><span data-stu-id="1d042-130">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>

## <a name="uploading-large-files-with-streaming"></a><span data-ttu-id="1d042-131">Téléchargement de fichiers volumineux avec la diffusion en continu</span><span class="sxs-lookup"><span data-stu-id="1d042-131">Uploading large files with streaming</span></span>

<span data-ttu-id="1d042-132">Si la taille ou la fréquence des téléchargements de fichiers pose des problèmes de ressources de l’application, envisagez le téléchargement du fichier de diffusion en continu plutôt que de mise en mémoire tampon elle dans son intégralité, contrairement à l’approche de liaison de modèle ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="1d042-132">If the size or frequency of file uploads is causing resource problems for the app, consider streaming the file upload rather than buffering it in its entirety, as the model binding approach shown above does.</span></span> <span data-ttu-id="1d042-133">Lors de l’utilisation `IFormFile` et liaison de modèle est une solution beaucoup plus simple, diffusion en continu requiert plusieurs étapes pour implémenter correctement.</span><span class="sxs-lookup"><span data-stu-id="1d042-133">While using `IFormFile` and model binding is a much simpler solution, streaming requires a number of steps to implement properly.</span></span>

> [!NOTE]
> <span data-ttu-id="1d042-134">N’importe quel fichier mis en mémoire tampon unique supérieure à 64 Ko est déplacé de la mémoire vive dans un fichier temporaire sur le disque sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="1d042-134">Any single buffered file exceeding 64KB will be moved from RAM to a temp file on disk on the server.</span></span> <span data-ttu-id="1d042-135">Les ressources (disque RAM) utilisées par les téléchargements de fichiers varient selon le nombre et la taille des téléchargements de fichier simultanées.</span><span class="sxs-lookup"><span data-stu-id="1d042-135">The resources (disk, RAM) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="1d042-136">Diffusion en continu n’est pas tellement sur les performances, il est sur l’échelle.</span><span class="sxs-lookup"><span data-stu-id="1d042-136">Streaming is not so much about perf, it's about scale.</span></span> <span data-ttu-id="1d042-137">Si vous essayez de mettre en mémoire tampon trop de téléchargements, votre site se bloque lorsqu’il s’exécute en dehors de la mémoire ou l’espace disque.</span><span class="sxs-lookup"><span data-stu-id="1d042-137">If you try to buffer too many uploads, your site will crash when it runs out of memory or disk space.</span></span>

<span data-ttu-id="1d042-138">L’exemple suivant illustre l’utilisation de JavaScript/angulaire à diffuser en continu à une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1d042-138">The following example demonstrates using JavaScript/Angular to stream to a controller action.</span></span> <span data-ttu-id="1d042-139">Jeton de côté du fichier est généré à l’aide d’un attribut de filtre personnalisé et passées dans les en-têtes HTTP et non dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="1d042-139">The file's antiforgery token is generated using a custom filter attribute and passed in HTTP headers instead of in the request body.</span></span> <span data-ttu-id="1d042-140">Étant donné que la méthode d’action traite les données transmises directement, liaison de modèle est désactivée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="1d042-140">Because the action method processes the uploaded data directly, model binding is disabled by another filter.</span></span> <span data-ttu-id="1d042-141">Dans l’action, le contenu du formulaire est lus en utilisant un `MultipartReader`, qui lit chaque `MultipartSection`, du traitement du fichier ou enregistrer le contenu comme il convient.</span><span class="sxs-lookup"><span data-stu-id="1d042-141">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="1d042-142">Une fois que toutes les sections ont été lus, l’action effectue ses propres liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="1d042-142">Once all sections have been read, the action performs its own model binding.</span></span>

<span data-ttu-id="1d042-143">L’action initiale charge le formulaire et enregistre un jeton de côté dans un cookie (via la `GenerateAntiforgeryTokenCookieForAjax` attribut) :</span><span class="sxs-lookup"><span data-stu-id="1d042-143">The initial action loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieForAjax` attribute):</span></span>

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

<span data-ttu-id="1d042-144">L’attribut utilise les intégrée d’ASP.NET Core [Antiforgery](xref:security/anti-request-forgery) prise en charge d’un cookie avec un jeton de demande :</span><span class="sxs-lookup"><span data-stu-id="1d042-144">The attribute uses ASP.NET Core's built-in [Antiforgery](xref:security/anti-request-forgery) support to set a cookie with a request token:</span></span>

<span data-ttu-id="1d042-145">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="1d042-145">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]</span></span>

<span data-ttu-id="1d042-146">Angulaire transmet automatiquement un jeton de côté dans un en-tête de demande nommé `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="1d042-146">Angular automatically passes an antiforgery token in a request header named `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="1d042-147">L’application ASP.NET MVC de base est configurée pour faire référence à cet en-tête dans sa configuration dans *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="1d042-147">The ASP.NET Core MVC app is configured to refer to this header in its configuration in *Startup.cs*:</span></span>

<span data-ttu-id="1d042-148">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="1d042-148">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]</span></span>

<span data-ttu-id="1d042-149">Le `DisableFormValueModelBinding` attribut, illustré ci-dessous, est utilisé pour désactiver la liaison de modèle pour le `Upload` méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="1d042-149">The `DisableFormValueModelBinding` attribute, shown below, is used to disable model binding for the `Upload` action method.</span></span>

<span data-ttu-id="1d042-150">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="1d042-150">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]</span></span>

<span data-ttu-id="1d042-151">Étant donné que la liaison de modèle est désactivée, le `Upload` méthode d’action n’accepte pas les paramètres.</span><span class="sxs-lookup"><span data-stu-id="1d042-151">Since model binding is disabled, the `Upload` action method doesn't accept parameters.</span></span> <span data-ttu-id="1d042-152">Il fonctionne directement avec le `Request` propriété du `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="1d042-152">It works directly with the `Request` property of `ControllerBase`.</span></span> <span data-ttu-id="1d042-153">A `MultipartReader` est utilisé pour lire chaque section.</span><span class="sxs-lookup"><span data-stu-id="1d042-153">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="1d042-154">Le fichier est enregistré avec un nom de fichier GUID et les données de clé/valeur sont stockées dans un `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="1d042-154">The file is saved with a GUID filename and the key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="1d042-155">Une fois que toutes les sections ont été lus, le contenu de la `KeyValueAccumulator` sont utilisés pour lier les données de formulaire à un type de modèle.</span><span class="sxs-lookup"><span data-stu-id="1d042-155">Once all sections have been read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="1d042-156">Le texte complet `Upload` méthode est indiquée ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="1d042-156">The complete `Upload` method is shown below:</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

<span data-ttu-id="1d042-157">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="1d042-157">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1d042-158">Résolution des problèmes</span><span class="sxs-lookup"><span data-stu-id="1d042-158">Troubleshooting</span></span>

<span data-ttu-id="1d042-159">Voici certains problèmes courants rencontrés lors de l’utilisation avec le téléchargement de fichiers et leurs solutions possibles.</span><span class="sxs-lookup"><span data-stu-id="1d042-159">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="unexpected-not-found-error-with-iis"></a><span data-ttu-id="1d042-160">Erreur inattendue introuvable avec IIS</span><span class="sxs-lookup"><span data-stu-id="1d042-160">Unexpected Not Found error with IIS</span></span>

<span data-ttu-id="1d042-161">L’erreur suivante indique le chargement du fichier dépasse le serveur configurée `maxAllowedContentLength`:</span><span class="sxs-lookup"><span data-stu-id="1d042-161">The following error indicates your file upload exceeds the server's configured `maxAllowedContentLength`:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="1d042-162">Le paramètre par défaut est `30000000`, qui est d’environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="1d042-162">The default setting is `30000000`, which is approximately 28.6MB.</span></span> <span data-ttu-id="1d042-163">La valeur peut être personnalisée en modifiant *web.config*:</span><span class="sxs-lookup"><span data-stu-id="1d042-163">The value can be customized by editing *web.config*:</span></span>

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

<span data-ttu-id="1d042-164">Ce paramètre s’applique uniquement à IIS.</span><span class="sxs-lookup"><span data-stu-id="1d042-164">This setting only applies to IIS.</span></span> <span data-ttu-id="1d042-165">Le comportement ne se produit par défaut lors de l’hébergement sur Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1d042-165">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="1d042-166">Pour plus d’informations, consultez [limites des demandes \<requestLimits\>](https://www.iis.net/configreference/system.webserver/security/requestfiltering/requestlimits).</span><span class="sxs-lookup"><span data-stu-id="1d042-166">For more information, see [Request Limits \<requestLimits\>](https://www.iis.net/configreference/system.webserver/security/requestfiltering/requestlimits).</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="1d042-167">Exception de référence null avec IFormFile</span><span class="sxs-lookup"><span data-stu-id="1d042-167">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="1d042-168">Si votre contrôleur est acceptant téléchargé à l’aide de fichiers `IFormFile` , mais vous découvrirez que la valeur est toujours null, vérifiez la spécification de votre formulaire HTML un `enctype` valeur `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="1d042-168">If your controller is accepting uploaded files using `IFormFile` but you find that the value is always null, confirm that your HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="1d042-169">Si cet attribut n’est pas défini sur le `<form>` élément, le téléchargement du fichier ne se produira pas et n’importe quelle limite `IFormFile` arguments est null.</span><span class="sxs-lookup"><span data-stu-id="1d042-169">If this attribute is not set on the `<form>` element, the file upload will not occur and any bound `IFormFile` arguments will be null.</span></span>
