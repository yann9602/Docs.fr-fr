---
uid: mvc/overview/older-versions-1/controllers-and-routing/adding-dynamic-content-to-a-cached-page-cs
title: "Ajout de contenu dynamique à une Page mise en cache (c#) | Documents Microsoft"
author: microsoft
description: "Découvrez comment combiner le contenu dynamique et mise en cache dans la même page. Post-cache vous permet d’afficher le contenu dynamique, tels que bannière publications o..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/27/2009
ms.topic: article
ms.assetid: 2ddd4407-d143-4a94-877c-21771bfb97a6
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/adding-dynamic-content-to-a-cached-page-cs
msc.type: authoredcontent
ms.openlocfilehash: bee7e17ee16d75419c215558b1deb7d6f0d79448
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-dynamic-content-to-a-cached-page-c"></a>Ajout de contenu dynamique à une Page mise en cache (c#)
====================
par [Microsoft](https://github.com/microsoft)

> Découvrez comment combiner le contenu dynamique et mise en cache dans la même page. Post-cache permet d’afficher du contenu dynamique, tels que des annonces ou des éléments de news, au sein d’une page qui a été sortie mise en cache.


En tirant parti de la mise en cache de sortie, vous pouvez améliorer considérablement les performances d’une application ASP.NET MVC. Au lieu de la régénération d’une page chaque fois que la page est demandée, la page peut être générée une fois et mis en cache en mémoire pour plusieurs utilisateurs.

Mais il existe un problème. Que se passe-t-il si vous avez besoin afficher le contenu dynamique dans la page ? Par exemple, imaginez que vous souhaitez afficher une publicité de bannière dans la page. Vous ne souhaitez pas que la publication de bannière doit être mis en cache afin que chaque utilisateur visualise la même publication. Vous partageraient argent de cette façon.

Heureusement, il existe une solution simple. Vous pouvez tirer parti d’une fonctionnalité de l’infrastructure ASP.NET appelé *post-cache*. Substitution post-cache vous permet de remplacer le contenu dynamique dans une page qui a été mis en cache en mémoire.


Normalement, lors de la sortie d’une page de cache à l’aide de l’attribut [OutputCache], la page est mise en cache sur le serveur et le client (navigateur web). Lorsque vous utilisez post-cache, une page est mise en cache uniquement sur le serveur.


#### <a name="using-post-cache-substitution"></a>À l’aide de la substitution post-cache

À l’aide de la substitution post-cache nécessite deux étapes. Tout d’abord, vous devez définir une méthode qui retourne une chaîne qui représente le contenu dynamique que vous souhaitez afficher dans la page mise en cache. Ensuite, vous appelez la méthode HttpResponse.WriteSubstitution() pour injecter du contenu dynamique dans la page.

Par exemple, imaginez que vous souhaitez afficher au hasard des actualités différents dans une page mise en cache. La classe dans la liste 1 expose une méthode unique nommée RenderNews(), qui aléatoirement retourne un élément d’information dans une liste de trois éléments d’actualité.

**La liste 1 – Models\News.cs**

[!code-csharp[Main](adding-dynamic-content-to-a-cached-page-cs/samples/sample1.cs)]

Pour tirer parti de substitution post-cache, vous appelez la méthode HttpResponse.WriteSubstitution(). La méthode WriteSubstitution() définit le code pour remplacer une région de la page mise en cache avec un contenu dynamique. La méthode WriteSubstitution() est utilisée pour afficher l’article aléatoire dans la vue dans la liste 2.

**Listing 2 – Views\Home\Index.aspx**

[!code-aspx[Main](adding-dynamic-content-to-a-cached-page-cs/samples/sample2.aspx)]

La méthode RenderNews est passée à la méthode WriteSubstitution(). Notez que la méthode RenderNews n’est pas appelée (il existe sans parenthèses). En revanche, une référence à la méthode est passée à WriteSubstitution().

La vue de l’Index est mis en cache. La vue est retournée par le contrôleur dans la liste 3. Notez que l’action Index() est décorée avec un attribut [OutputCache] qui provoque l’affichage de l’Index doit être mis en cache pendant 60 secondes.

**La liste 3 – Controllers\HomeController.cs**

[!code-csharp[Main](adding-dynamic-content-to-a-cached-page-cs/samples/sample3.cs)]

Même si la vue de l’Index est mis en cache, les éléments de news aléatoires différents sont affichés lorsque vous demandez la page d’Index. Lorsque vous demandez la page d’Index, l’heure affichée dans la page ne change pas pendant 60 secondes (voir Figure 1). Le fait que l’heure ne change pas afin de garantir que la page est mise en cache. Toutefois, le contenu injectées par les modifications de la méthode : l’élément aléatoire – WriteSubstitution() avec chaque demande.

**Figure 1 – injection actualités dynamique dans une page mise en cache**

![clip_image002](adding-dynamic-content-to-a-cached-page-cs/_static/image1.jpg)

#### <a name="using-post-cache-substitution-in-helper-methods"></a>À l’aide de la substitution post-cache dans les méthodes d’assistance

Un moyen plus simple de tirer parti de substitution post-cache est d’encapsuler l’appel à la méthode WriteSubstitution() au sein d’une méthode d’assistance personnalisé. Cette approche est illustrée par la méthode d’assistance dans la liste 4.

**La liste 4 – AdHelper.cs**

[!code-csharp[Main](adding-dynamic-content-to-a-cached-page-cs/samples/sample4.cs)]

La liste 4 contient une classe statique qui expose deux méthodes : RenderBanner() et RenderBannerInternal(). La méthode RenderBanner() représente la méthode d’assistance réel. Cette méthode étend la classe HtmlHelper de MVC ASP.NET standard qui vous pouvez d’appeler Html.RenderBanner() dans une vue comme toute autre méthode d’assistance.

La méthode RenderBanner() appelle la méthode de HttpResponse.WriteSubstitution() en passant de la méthode RenderBannerInternal() à la méthode WriteSubsitution().

La méthode RenderBannerInternal() est une méthode privée. Cette méthode ne sera pas exposée comme une méthode d’assistance. La méthode RenderBannerInternal() retourne aléatoirement une bannière de publication dans la liste des trois images de publication de bannière.

La vue Index modifiée dans la liste 5 illustre comment vous pouvez utiliser la méthode d’assistance RenderBanner(). Notez que supplémentaire &lt;% @ Import %&gt; la directive est incluse en haut de la vue à importer l’espace de noms MvcApplication1.Helpers. Si vous oubliez d’importer cet espace de noms, la méthode RenderBanner() ne s’affiche comme une méthode sur la propriété Html.

**La liste 5 – Views\Home\Index.aspx (avec la méthode RenderBanner())**

[!code-aspx[Main](adding-dynamic-content-to-a-cached-page-cs/samples/sample5.aspx)]

Lorsque vous demandez la page rendue par la vue dans la liste 5, une publication de bannière différent est affichée avec chaque requête (voir Figure 2). La page est mise en cache, mais la publication de la bannière est injectée dynamiquement par la méthode d’assistance RenderBanner().

**Figure 2 : la vue Index affichant une publication de bannière aléatoire**

![clip_image004](adding-dynamic-content-to-a-cached-page-cs/_static/image2.jpg)

#### <a name="summary"></a>Résumé

Ce didacticiel explique comment vous pouvez mettre à jour dynamiquement le contenu dans une page mise en cache. Vous avez appris comment utiliser la méthode HttpResponse.WriteSubstitution() pour permettre à injecter dans une page mise en cache du contenu dynamique. Vous avez également appris à encapsuler l’appel à la méthode WriteSubstitution() dans une méthode d’assistance HTML.

Tirer parti de la mise en cache chaque fois que possible : il peut avoir un impact considérable sur les performances de vos applications web. Comme expliqué dans ce didacticiel, vous pouvez tirer parti de la mise en cache même si vous avez besoin afficher le contenu dynamique dans vos pages.

## 

## 

>[!div class="step-by-step"]
[Précédent](improving-performance-with-output-caching-cs.md)
[Suivant](creating-a-controller-cs.md)
