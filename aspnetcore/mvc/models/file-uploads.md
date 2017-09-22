---
title: "Téléchargements de fichiers dans ASP.NET Core"
author: ardalis
description: "Comment utiliser la liaison de modèle et de diffusion en continu pour télécharger des fichiers dans ASP.NET MVC de base."
keywords: "ASP.NET Core, le téléchargement du fichier de modèle de liaison, IFormFile, diffusion en continu"
ms.author: riande
manager: wpickett
ms.date: 07/05/2017
ms.topic: article
ms.assetid: ebc98159-a028-4a94-b06c-43981c79c6be
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/file-uploads
ms.openlocfilehash: e8608a46d6688df8da6c665a25b6f4db5f480461
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="file-uploads-in-aspnet-core"></a><span data-ttu-id="82010-104">Téléchargements de fichiers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82010-104">File uploads in ASP.NET Core</span></span>

<span data-ttu-id="82010-105">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="82010-105">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="82010-106">Actions d’ASP.NET MVC prend en charge le téléchargement d’un ou plusieurs fichiers à l’aide d’un modèle simple pour les fichiers plus petits de liaison ou de diffusion en continu pour les fichiers plus volumineux.</span><span class="sxs-lookup"><span data-stu-id="82010-106">ASP.NET MVC actions support uploading of one or more files using simple model binding for smaller files or streaming for larger files.</span></span>

[<span data-ttu-id="82010-107">Afficher ou télécharger l’exemple à partir de GitHub</span><span class="sxs-lookup"><span data-stu-id="82010-107">View or download sample from GitHub</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a><span data-ttu-id="82010-108">Téléchargement de petits fichiers avec la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="82010-108">Uploading small files with model binding</span></span>

<span data-ttu-id="82010-109">Pour télécharger des petits fichiers, vous pouvez utiliser un formulaire HTML plusieurs partie ou construire une demande POST à l’aide de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="82010-109">To upload small files, you can use a multi-part HTML form or construct a POST request using JavaScript.</span></span> <span data-ttu-id="82010-110">Un exemple de formulaire à l’aide de Razor, qui prend en charge plusieurs fichiers téléchargés, est indiqué ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="82010-110">An example form using Razor, which supports multiple uploaded files, is shown below:</span></span>

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

<span data-ttu-id="82010-111">Pour prendre en charge les téléchargements de fichiers, les formulaires HTML doivent spécifier un `enctype` de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="82010-111">In order to support file uploads, HTML forms must specify an `enctype` of `multipart/form-data`.</span></span> <span data-ttu-id="82010-112">Le `files` élément d’entrée ci-dessus prend en charge le téléchargement de plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="82010-112">The `files` input element shown above supports uploading multiple files.</span></span> <span data-ttu-id="82010-113">Omettre la `multiple` attribut de cet élément d’entrée pour autoriser uniquement un seul fichier à télécharger.</span><span class="sxs-lookup"><span data-stu-id="82010-113">Omit the `multiple` attribute on this input element to allow just a single file to be uploaded.</span></span> <span data-ttu-id="82010-114">Le balisage ci-dessus est rendu dans un navigateur en tant que :</span><span class="sxs-lookup"><span data-stu-id="82010-114">The above markup renders in a browser as:</span></span>

![Formulaire de téléchargement de fichier](file-uploads/_static/upload-form.png)

<span data-ttu-id="82010-116">Les fichiers individuels téléchargés sur le serveur est accessible via [liaison de modèle](xref:mvc/models/model-binding) à l’aide de la [IFormFile](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.iformfile) interface.</span><span class="sxs-lookup"><span data-stu-id="82010-116">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using the [IFormFile](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.iformfile) interface.</span></span> <span data-ttu-id="82010-117">`IFormFile`a la structure suivante :</span><span class="sxs-lookup"><span data-stu-id="82010-117">`IFormFile` has the following structure:</span></span>

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
> <span data-ttu-id="82010-118">Ne s’appuient sur ou approuver le `FileName` propriété sans validation.</span><span class="sxs-lookup"><span data-stu-id="82010-118">Don't rely on or trust the `FileName` property without validation.</span></span> <span data-ttu-id="82010-119">Le `FileName` propriété doit uniquement être utilisée pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="82010-119">The `FileName` property should only be used for display purposes.</span></span>

<span data-ttu-id="82010-120">Lors du téléchargement de fichiers à l’aide de la liaison de modèle et la `IFormFile` interface, la méthode d’action peut accepter un seul `IFormFile` ou `IEnumerable<IFormFile>` (ou `List<IFormFile>`) représentant plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="82010-120">When uploading files using model binding and the `IFormFile` interface, the action method can accept either a single `IFormFile` or an `IEnumerable<IFormFile>` (or `List<IFormFile>`) representing several files.</span></span> <span data-ttu-id="82010-121">L’exemple suivant effectue une itération sur un ou plusieurs des fichiers téléchargés, les enregistre dans le système de fichiers local et retourne le nombre total et la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="82010-121">The following example loops through one or more uploaded files, saves them to the local file system, and returns the total number and size of files uploaded.</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

<span data-ttu-id="82010-122">Les fichiers téléchargés à l’aide de la `IFormFile` technique sont mis en mémoire tampon en mémoire ou sur disque sur le serveur web avant d’être traité.</span><span class="sxs-lookup"><span data-stu-id="82010-122">Files uploaded using the `IFormFile` technique are buffered in memory or on disk on the web server before being processed.</span></span> <span data-ttu-id="82010-123">À l’intérieur de la méthode d’action, le `IFormFile` contenu est accessible en tant que flux.</span><span class="sxs-lookup"><span data-stu-id="82010-123">Inside the action method, the `IFormFile` contents are accessible as a stream.</span></span> <span data-ttu-id="82010-124">Outre le système de fichiers local, des fichiers peuvent être diffusées à [le stockage Blob Azure](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs/) ou [Entity Framework](https://docs.microsoft.com/ef/core/index).</span><span class="sxs-lookup"><span data-stu-id="82010-124">In addition to the local file system, files can be streamed to [Azure Blob storage](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs/) or [Entity Framework](https://docs.microsoft.com/ef/core/index).</span></span>

<span data-ttu-id="82010-125">Pour stocker les données de fichier binaire dans une base de données à l’aide d’Entity Framework, définir une propriété de type `byte[]` sur l’entité :</span><span class="sxs-lookup"><span data-stu-id="82010-125">To store binary file data in a database using Entity Framework, define a property of type `byte[]` on the entity:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

<span data-ttu-id="82010-126">Spécifiez une propriété viewmodel de type `IFormFile`:</span><span class="sxs-lookup"><span data-stu-id="82010-126">Specify a viewmodel property of type `IFormFile`:</span></span>

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="82010-127">`IFormFile`peut être utilisé directement comme un paramètre de méthode d’action ou une propriété viewmodel, comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="82010-127">`IFormFile` can be used directly as an action method parameter or as a viewmodel property, as shown above.</span></span>

<span data-ttu-id="82010-128">Copie le `IFormFile` dans un flux et enregistrez-le dans le tableau d’octets :</span><span class="sxs-lookup"><span data-stu-id="82010-128">Copy the `IFormFile` to a stream and save it to the byte array:</span></span>

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
> <span data-ttu-id="82010-129">Soyez prudent lorsque vous stockez des données binaires dans les bases de données relationnelles, comme il peut nuire aux performances.</span><span class="sxs-lookup"><span data-stu-id="82010-129">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>

## <a name="uploading-large-files-with-streaming"></a><span data-ttu-id="82010-130">Téléchargement de fichiers volumineux avec la diffusion en continu</span><span class="sxs-lookup"><span data-stu-id="82010-130">Uploading large files with streaming</span></span>

<span data-ttu-id="82010-131">Si la taille ou la fréquence des téléchargements de fichiers pose des problèmes de ressources de l’application, envisagez le téléchargement du fichier de diffusion en continu plutôt que de mise en mémoire tampon elle dans son intégralité, contrairement à l’approche de liaison de modèle ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="82010-131">If the size or frequency of file uploads is causing resource problems for the app, consider streaming the file upload rather than buffering it in its entirety, as the model binding approach shown above does.</span></span> <span data-ttu-id="82010-132">Lors de l’utilisation `IFormFile` et liaison de modèle est une solution beaucoup plus simple, diffusion en continu requiert plusieurs étapes pour implémenter correctement.</span><span class="sxs-lookup"><span data-stu-id="82010-132">While using `IFormFile` and model binding is a much simpler solution, streaming requires a number of steps to implement properly.</span></span>

> [!NOTE]
> <span data-ttu-id="82010-133">N’importe quel fichier mis en mémoire tampon unique supérieure à 64 Ko est déplacé de la mémoire vive dans un fichier temporaire sur le disque sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="82010-133">Any single buffered file exceeding 64KB will be moved from RAM to a temp file on disk on the server.</span></span> <span data-ttu-id="82010-134">Les ressources (disque RAM) utilisées par les téléchargements de fichiers varient selon le nombre et la taille des téléchargements de fichier simultanées.</span><span class="sxs-lookup"><span data-stu-id="82010-134">The resources (disk, RAM) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="82010-135">Diffusion en continu n’est pas tellement sur les performances, il est sur l’échelle.</span><span class="sxs-lookup"><span data-stu-id="82010-135">Streaming is not so much about perf, it's about scale.</span></span> <span data-ttu-id="82010-136">Si vous essayez de mettre en mémoire tampon trop de téléchargements, votre site se bloque lorsqu’il s’exécute en dehors de la mémoire ou l’espace disque.</span><span class="sxs-lookup"><span data-stu-id="82010-136">If you try to buffer too many uploads, your site will crash when it runs out of memory or disk space.</span></span>

<span data-ttu-id="82010-137">L’exemple suivant illustre l’utilisation de JavaScript/angulaire à diffuser en continu à une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="82010-137">The following example demonstrates using JavaScript/Angular to stream to a controller action.</span></span> <span data-ttu-id="82010-138">Jeton de côté du fichier est généré à l’aide d’un attribut de filtre personnalisé et passées dans les en-têtes HTTP et non dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="82010-138">The file's antiforgery token is generated using a custom filter attribute and passed in HTTP headers instead of in the request body.</span></span> <span data-ttu-id="82010-139">Étant donné que la méthode d’action traite les données transmises directement, liaison de modèle est désactivée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="82010-139">Because the action method processes the uploaded data directly, model binding is disabled by another filter.</span></span> <span data-ttu-id="82010-140">Dans l’action, le contenu du formulaire est lus en utilisant un `MultipartReader`, qui lit chaque `MultipartSection`, du traitement du fichier ou enregistrer le contenu comme il convient.</span><span class="sxs-lookup"><span data-stu-id="82010-140">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="82010-141">Une fois que toutes les sections ont été lus, l’action effectue ses propres liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="82010-141">Once all sections have been read, the action performs its own model binding.</span></span>

<span data-ttu-id="82010-142">L’action initiale charge le formulaire et enregistre un jeton de côté dans un cookie (via la `GenerateAntiforgeryTokenCookieForAjax` attribut) :</span><span class="sxs-lookup"><span data-stu-id="82010-142">The initial action loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieForAjax` attribute):</span></span>

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

<span data-ttu-id="82010-143">L’attribut utilise les intégrée d’ASP.NET Core [Antiforgery](xref:security/anti-request-forgery) prise en charge d’un cookie avec un jeton de demande :</span><span class="sxs-lookup"><span data-stu-id="82010-143">The attribute uses ASP.NET Core's built-in [Antiforgery](xref:security/anti-request-forgery) support to set a cookie with a request token:</span></span>

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

<span data-ttu-id="82010-144">Angulaire transmet automatiquement un jeton de côté dans un en-tête de demande nommé `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="82010-144">Angular automatically passes an antiforgery token in a request header named `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="82010-145">L’application ASP.NET MVC de base est configurée pour faire référence à cet en-tête dans sa configuration dans *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="82010-145">The ASP.NET Core MVC app is configured to refer to this header in its configuration in *Startup.cs*:</span></span>

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

<span data-ttu-id="82010-146">Le `DisableFormValueModelBinding` attribut, illustré ci-dessous, est utilisé pour désactiver la liaison de modèle pour le `Upload` méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="82010-146">The `DisableFormValueModelBinding` attribute, shown below, is used to disable model binding for the `Upload` action method.</span></span>

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

<span data-ttu-id="82010-147">Étant donné que la liaison de modèle est désactivée, le `Upload` méthode d’action n’accepte pas les paramètres.</span><span class="sxs-lookup"><span data-stu-id="82010-147">Since model binding is disabled, the `Upload` action method doesn't accept parameters.</span></span> <span data-ttu-id="82010-148">Il fonctionne directement avec le `Request` propriété du `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="82010-148">It works directly with the `Request` property of `ControllerBase`.</span></span> <span data-ttu-id="82010-149">A `MultipartReader` est utilisé pour lire chaque section.</span><span class="sxs-lookup"><span data-stu-id="82010-149">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="82010-150">Le fichier est enregistré avec un nom de fichier GUID et les données de clé/valeur sont stockées dans un `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="82010-150">The file is saved with a GUID filename and the key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="82010-151">Une fois que toutes les sections ont été lus, le contenu de la `KeyValueAccumulator` sont utilisés pour lier les données de formulaire à un type de modèle.</span><span class="sxs-lookup"><span data-stu-id="82010-151">Once all sections have been read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="82010-152">Le texte complet `Upload` méthode est indiquée ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="82010-152">The complete `Upload` method is shown below:</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a><span data-ttu-id="82010-153">Résolution des problèmes</span><span class="sxs-lookup"><span data-stu-id="82010-153">Troubleshooting</span></span>

<span data-ttu-id="82010-154">Voici certains problèmes courants rencontrés lors de l’utilisation avec le téléchargement de fichiers et leurs solutions possibles.</span><span class="sxs-lookup"><span data-stu-id="82010-154">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="unexpected-not-found-error-with-iis"></a><span data-ttu-id="82010-155">Erreur inattendue introuvable avec IIS</span><span class="sxs-lookup"><span data-stu-id="82010-155">Unexpected Not Found error with IIS</span></span>

<span data-ttu-id="82010-156">L’erreur suivante indique le chargement du fichier dépasse le serveur configurée `maxAllowedContentLength`:</span><span class="sxs-lookup"><span data-stu-id="82010-156">The following error indicates your file upload exceeds the server's configured `maxAllowedContentLength`:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="82010-157">Le paramètre par défaut est `30000000`, qui est d’environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="82010-157">The default setting is `30000000`, which is approximately 28.6MB.</span></span> <span data-ttu-id="82010-158">La valeur peut être personnalisée en modifiant *web.config*:</span><span class="sxs-lookup"><span data-stu-id="82010-158">The value can be customized by editing *web.config*:</span></span>

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

<span data-ttu-id="82010-159">Ce paramètre s’applique uniquement à IIS.</span><span class="sxs-lookup"><span data-stu-id="82010-159">This setting only applies to IIS.</span></span> <span data-ttu-id="82010-160">Le comportement ne se produit par défaut lors de l’hébergement sur Kestrel.</span><span class="sxs-lookup"><span data-stu-id="82010-160">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="82010-161">Pour plus d’informations, consultez [limites des demandes \<requestLimits\>](https://docs.microsoft.com/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="82010-161">For more information, see [Request Limits \<requestLimits\>](https://docs.microsoft.com/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="82010-162">Exception de référence null avec IFormFile</span><span class="sxs-lookup"><span data-stu-id="82010-162">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="82010-163">Si votre contrôleur est acceptant téléchargé à l’aide de fichiers `IFormFile` , mais vous découvrirez que la valeur est toujours null, vérifiez la spécification de votre formulaire HTML un `enctype` valeur `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="82010-163">If your controller is accepting uploaded files using `IFormFile` but you find that the value is always null, confirm that your HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="82010-164">Si cet attribut n’est pas défini sur le `<form>` élément, le téléchargement du fichier ne se produira pas et n’importe quelle limite `IFormFile` arguments est null.</span><span class="sxs-lookup"><span data-stu-id="82010-164">If this attribute is not set on the `<form>` element, the file upload will not occur and any bound `IFormFile` arguments will be null.</span></span>
