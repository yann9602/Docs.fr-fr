---
title: "Application d’assistance de balise d’image | Documents Microsoft"
author: pkellner
description: "Montre comment travailler avec l’Image, balise d’assistance"
keywords: ASP.NET Core,tag helper
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: c045d485-d1dc-4cea-a675-46be83b7a013
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/ImageTagHelper
ms.openlocfilehash: e91018be7d706ddc227f82b695a188ed91163f9d
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2017
---
# <a name="imagetaghelper"></a>ImageTagHelper

Par [Peter Kellner](http://peterkellner.net) 

L’application d’assistance de balise Image améliore la `img` (`<img>`) balise. Il nécessite un `src` balise, ainsi que les `boolean` attribut `asp-append-version`.

Si la source d’image (`src`) est un fichier statique sur le serveur web hôte, un cache unique fonctions antispam chaîne est ajouté en tant que paramètre de requête à la source de l’image. Cela garantit que si le fichier sur le serveur web hôte change, une URL de demande unique est générée qui inclut le paramètre de demande de mise à jour. Le cache fonctions antispam chaîne est une valeur unique représentant le hachage du fichier image statique.

Si la source d’image (`src`) n’est pas un fichier statique (par exemple une URL distante ou le fichier n’existe pas sur le serveur), le `<img>` la balise `src` attribut est généré sans cache fonctions antispam de paramètre de chaîne de requête.

## <a name="image-tag-helper-attributes"></a>Attributs d’assistance balises d’image


### <a name="asp-append-version"></a>ASP-ajouter-version

Lorsque spécifié avec un `src` attribut, l’assistance de balise d’Image est appelé.

Un exemple de valide `img` d’assistance de balise est :

```cshtml
<img src="~/images/asplogo.png" 
    asp-append-version="true"  />
```

Si le fichier statique existe dans le répertoire *... wwwroot/images/asplogo.png* le code html généré est semblable au suivant (le hachage sera différent) :

```html
<img 
    src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM"/>
```

La valeur affectée au paramètre `v` est la valeur de hachage de fichier sur le disque. Si le serveur web ne peut pas obtenir l’accès en lecture au fichier statique référencée, non `v` paramètres est ajouté à la `src` attribut.

- - -

### <a name="src"></a>src

Pour activer l’assistance de balise d’Image, l’attribut src est requis sur le `<img>` élément. 

> [!NOTE]
> L’assistance de balise d’Image utilise le `Cache` fournisseur sur le serveur web local pour stocker le texte calculé `Sha512` d’un fichier donné. Si le fichier est demandé à nouveau le `Sha512` ne doivent pas être recalculées. Le Cache est invalidé par un observateur de fichier qui est associé au fichier lors du fichier `Sha512` est calculée.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:performance/caching/memory>
