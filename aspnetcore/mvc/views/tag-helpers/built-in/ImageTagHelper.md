---
title: "Application d’assistance de balise d’image | Documents Microsoft"
author: pkellner
description: "Montre comment travailler avec l’Image, balise d’assistance"
keywords: "ASP.NET Core, d’assistance de balise"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: c045d485-d1dc-4cea-a675-46be83b7a013
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/ImageTagHelper
ms.openlocfilehash: 67537674154d885fc6f69accd2cc7f01c9104d71
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="imagetaghelper"></a><span data-ttu-id="289c9-104">ImageTagHelper</span><span class="sxs-lookup"><span data-stu-id="289c9-104">ImageTagHelper</span></span>

<span data-ttu-id="289c9-105">Par [Peter Kellner](http://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="289c9-105">By [Peter Kellner](http://peterkellner.net)</span></span> 

<span data-ttu-id="289c9-106">L’application d’assistance de balise Image améliore la `img` (`<img>`) balise.</span><span class="sxs-lookup"><span data-stu-id="289c9-106">The Image Tag Helper enhances the `img` (`<img>`) tag.</span></span> <span data-ttu-id="289c9-107">Il nécessite un `src` balise, ainsi que les `boolean` attribut `asp-append-version`.</span><span class="sxs-lookup"><span data-stu-id="289c9-107">It requires a `src` tag as well as the `boolean` attribute `asp-append-version`.</span></span>

<span data-ttu-id="289c9-108">Si la source d’image (`src`) est un fichier statique sur le serveur web hôte, un cache unique fonctions antispam chaîne est ajouté en tant que paramètre de requête à la source de l’image.</span><span class="sxs-lookup"><span data-stu-id="289c9-108">If the image source (`src`) is a static file on the host web server, a unique cache busting string is appended as a query parameter to the image source.</span></span> <span data-ttu-id="289c9-109">Cela garantit que si le fichier sur le serveur web hôte change, une URL de demande unique est générée qui inclut le paramètre de demande de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="289c9-109">This ensures that if the file on the host web server changes, a unique request URL is generated that includes the updated request parameter.</span></span> <span data-ttu-id="289c9-110">Le cache fonctions antispam chaîne est une valeur unique représentant le hachage du fichier image statique.</span><span class="sxs-lookup"><span data-stu-id="289c9-110">The cache busting string is a unique value representing the hash of the static image file.</span></span>

<span data-ttu-id="289c9-111">Si la source d’image (`src`) n’est pas un fichier statique (par exemple une URL distante ou le fichier n’existe pas sur le serveur), le `<img>` la balise `src` attribut est généré sans cache fonctions antispam de paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="289c9-111">If the image source (`src`) isn't a static file (for example a remote URL or the file doesn't exist on the server), the `<img>` tag's `src` attribute is generated with no cache busting query string parameter.</span></span>

## <a name="image-tag-helper-attributes"></a><span data-ttu-id="289c9-112">Attributs d’assistance balises d’image</span><span class="sxs-lookup"><span data-stu-id="289c9-112">Image Tag Helper Attributes</span></span>


### <a name="asp-append-version"></a><span data-ttu-id="289c9-113">ASP-ajouter-version</span><span class="sxs-lookup"><span data-stu-id="289c9-113">asp-append-version</span></span>

<span data-ttu-id="289c9-114">Lorsque spécifié avec un `src` attribut, l’assistance de balise d’Image est appelé.</span><span class="sxs-lookup"><span data-stu-id="289c9-114">When specified along with a `src` attribute, the Image Tag Helper is invoked.</span></span>

<span data-ttu-id="289c9-115">Un exemple de valide `img` d’assistance de balise est :</span><span class="sxs-lookup"><span data-stu-id="289c9-115">An example of a valid `img` tag helper is:</span></span>

```cshtml
<img src="~/images/asplogo.png" 
    asp-append-version="true"  />
```

<span data-ttu-id="289c9-116">Si le fichier statique existe dans le répertoire *... wwwroot/images/asplogo.png* le code html généré est semblable au suivant (le hachage sera différent) :</span><span class="sxs-lookup"><span data-stu-id="289c9-116">If the static file exists in the directory *..wwwroot/images/asplogo.png* the generated html is similar to the following (the hash will be different):</span></span>

```html
<img 
    src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM"/>
```

<span data-ttu-id="289c9-117">La valeur affectée au paramètre `v` est la valeur de hachage de fichier sur le disque.</span><span class="sxs-lookup"><span data-stu-id="289c9-117">The value assigned to the parameter `v` is the hash value of the file on disk.</span></span> <span data-ttu-id="289c9-118">Si le serveur web ne peut pas obtenir l’accès en lecture au fichier statique référencée, non `v` paramètres est ajouté à la `src` attribut.</span><span class="sxs-lookup"><span data-stu-id="289c9-118">If the web server is unable to obtain read access to the static file referenced,  no `v` parameters is added to the `src` attribute.</span></span>

- - -

### <a name="src"></a><span data-ttu-id="289c9-119">src</span><span class="sxs-lookup"><span data-stu-id="289c9-119">src</span></span>

<span data-ttu-id="289c9-120">Pour activer l’assistance de balise d’Image, l’attribut src est requis sur le `<img>` élément.</span><span class="sxs-lookup"><span data-stu-id="289c9-120">To activate the Image Tag Helper, the src attribute is required on the `<img>` element.</span></span> 

> [!NOTE]
> <span data-ttu-id="289c9-121">L’assistance de balise d’Image utilise le `Cache` fournisseur sur le serveur web local pour stocker le texte calculé `Sha512` d’un fichier donné.</span><span class="sxs-lookup"><span data-stu-id="289c9-121">The Image Tag Helper uses the `Cache` provider on the local web server to store the calculated `Sha512` of a given file.</span></span> <span data-ttu-id="289c9-122">Si le fichier est demandé à nouveau le `Sha512` ne doivent pas être recalculées.</span><span class="sxs-lookup"><span data-stu-id="289c9-122">If the file is requested again the `Sha512` does not need to be recalculated.</span></span> <span data-ttu-id="289c9-123">Le Cache est invalidé par un observateur de fichier qui est associé au fichier lors du fichier `Sha512` est calculée.</span><span class="sxs-lookup"><span data-stu-id="289c9-123">The Cache is invalidated by a file watcher that is attached to the file when the file's `Sha512` is calculated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="289c9-124">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="289c9-124">Additional resources</span></span>

* <xref:performance/caching/memory>