---
uid: web-pages/overview/routing/creating-readable-urls-in-aspnet-web-pages-sites
title: "Création d’URL lisibles dans ASP.NET Web Pages (Razor) Sites | Documents Microsoft"
author: tfitzmac
description: "Cet article décrit le routage dans un site Web ASP.NET Web Pages (Razor), et comment vous pouvez ainsi utiliser des URL qui sont plus lisible et mieux pour les moteurs de recherche. Vous allez..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/17/2014
ms.topic: article
ms.assetid: a8aac1ac-89de-4415-afe0-97a41c6423d2
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/routing/creating-readable-urls-in-aspnet-web-pages-sites
msc.type: authoredcontent
ms.openlocfilehash: 7858b7cbd6dccafb2867ed9a1d102561e211e435
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-readable-urls-in-aspnet-web-pages-razor-sites"></a>Création d’URL lisibles dans les Sites ASP.NET Web Pages (Razor)
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article décrit le routage dans un site Web ASP.NET Web Pages (Razor), et comment vous pouvez ainsi utiliser des URL qui sont plus lisible et mieux pour les moteurs de recherche.
> 
> Ce que vous allez apprendre :
> 
> - Comment ASP.NET utilise le routage pour vous permettre d’utiliser des URL plus lisibles et de recherches.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 3
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2.


## <a name="about-routing"></a>À propos du routage

Les URL pour les pages de votre site peuvent avoir un impact sur la manière dont le site fonctionne. Une URL de &quot;convivial&quot; peut rendre plus faciles à utiliser le site. Il peut également aider à l’optimisation de moteur de recherche (SEO) pour le site. Les sites Web ASP.NET incluent la possibilité d’utiliser des URL conviviales automatiquement.

ASP.NET vous permet de créer des URL significatives qui décrivent les actions de l’utilisateur au lieu de simplement en pointant vers un fichier sur le serveur. Prenons les URL pour un blog fictif :

- `http://www.contoso.com/Blog/blog.cshtml?categories=hardware`
- `http://www.contoso.com//Blog/blog.cshtml?startdate=2009-11-01&enddate=2009-11-30`

Comparez les URL pour les options suivantes :

- `http://www.contoso.com/Blog/categories/hardware/`
- `http://www.contoso.com/Blog/2009/November`

Dans la première paire, un utilisateur est obligé de savoir que le blog est affiché à l’aide de la *blog.cshtml* page et puis auraient construire une chaîne de requête qui obtient la plage de catégorie ou la date à droite. Le deuxième ensemble d’exemples est beaucoup plus facile à comprendre et à créer.

Les URL pour le premier exemple également pointent directement vers un fichier spécifique (*blog.cshtml*). Si pour une raison quelconque le blog ont été déplacé vers un autre dossier sur le serveur, ou si le blog ont été réécrites pour utiliser une autre page, les liens serait incorrects. Le deuxième ensemble de l’URL ne pointe pas vers une page spécifique, par conséquent, même si l’emplacement ou le blog de l’implémentation change, l’URL serait toujours valides.

Dans ASP.NET Web Pages, vous pouvez créer une URL conviviale telles que celles dans les exemples ci-dessus, car ASP.NET utilise *routage*. Routage de crée le mappage logique à partir d’une URL vers une page (ou de pages) qui peut répondre à la demande. Étant donné que le mappage est logique (non physique, dans un fichier spécifique), le routage fournit une grande souplesse dans la façon dont vous définissez l’URL de votre site.

## <a name="how-routing-works"></a>Fonctionnement du routage

Quand ASP.NET traite une demande, il lit l’URL pour déterminer comment router. ASP.NET essaie de faire correspondre les segments individuels de l’URL à des fichiers sur disque, allant de gauche à droite. S’il existe une correspondance, quoi que ce soit dans l’URL est passé à la page en tant que *les informations de chemin d’accès*.

Imaginez que quelqu'un effectue une demande à l’aide de cette URL :

`http://www.contoso.com/a/b/c`

La recherche est la suivante :

1. Existe-t-il un fichier avec le chemin d’accès et le nom de */a/b/c.cshtml*? Dans ce cas, exécutez cette page et lui ne passer aucune information. Sinon...
2. Existe-t-il un fichier avec le chemin d’accès et le nom de */a/b.cshtml*? Si par conséquent, cette page d’exécution et transmettre la valeur `c` à celui-ci. Sinon...
3. Existe-t-il un fichier avec le chemin d’accès et le nom de */a.cshtml*? Si par conséquent, cette page d’exécution et transmettre la valeur `b/c` à celui-ci.

Si la recherche a détecté exacte ne correspond pour *.cshtml* fichiers dans leurs dossiers spécifiés, ASP.NET continue à son tour la recherche de ces fichiers :

1. */a/b/c/default.cshtml* (aucune information de chemin d’accès).
2. */a/b/c/index.cshtml* (aucune information de chemin d’accès).

> [!NOTE]
> Pour être clair, demandes pour des pages spécifiques (autrement dit, les demandes qui incluent le *.cshtml* extension de nom de fichier) fonctionnent comme vous vous attendez. Une demande comme `http://www.contoso.com/a/b.cshtml` exécutera la page *b.cshtml* tout à fait correctement.


À l’intérieur d’une page, vous pouvez obtenir les informations de chemin d’accès par le biais de la page `UrlData` propriété, qui est un dictionnaire. Imaginez que vous disposez d’un fichier nommé *ViewCustomers.cshtml* et votre site obtient cette demande :

`http://mysite.com/myWebSite/ViewCustomers/1000`

Comme décrit dans les règles ci-dessus, la demande est transmise à votre page. À l’intérieur de la page, vous pouvez utiliser le code suivant pour obtenir et afficher les informations de chemin d’accès (dans ce cas, la valeur &quot;1000&quot;) :

[!code-html[Main](creating-readable-urls-in-aspnet-web-pages-sites/samples/sample1.html)]

> [!NOTE]
> Étant donné que le routage n’implique pas les noms de fichier complet, il peut y avoir une ambiguïté si vous avez des pages qui ont le même nom mais des extensions de nom de fichier (par exemple, *MyPage.cshtml* et *MyPage.html*) . Afin d’éviter des problèmes de routage, il est préférable de s’assurer que vous n’avez pas les pages de votre site dont les noms diffèrent uniquement par leur extension.


<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires

[WebMatrix - URL, de routage pour les moteurs de recherche et UrlData](http://www.mikesdotnetting.com/Article/165/WebMatrix-URLs-UrlData-and-Routing-for-SEO). Cette entrée de blog par Mike Brind fournit des informations supplémentaires sur le fonctionnement du routage dans ASP.NET Web Pages.
