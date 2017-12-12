---
uid: mvc/overview/older-versions-1/controllers-and-routing/improving-performance-with-output-caching-vb
title: "Amélioration des performances avec sortie mise en cache (VB) | Documents Microsoft"
author: microsoft
description: "Dans ce didacticiel, vous découvrez comment vous pouvez améliorer considérablement les performances des applications web ASP.NET MVC en tirant parti de la mise en cache de sortie. Vous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/27/2009
ms.topic: article
ms.assetid: 0e7b4d85-2c46-4eaf-b6a8-6cd566a67334
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/improving-performance-with-output-caching-vb
msc.type: authoredcontent
ms.openlocfilehash: 3bd4b6c3ac52577cbee451d2986f1167e441f0e6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="improving-performance-with-output-caching-vb"></a>Amélioration des performances avec la sortie mise en cache (VB)
====================
par [Microsoft](https://github.com/microsoft)

> Dans ce didacticiel, vous découvrez comment vous pouvez améliorer considérablement les performances des applications web ASP.NET MVC en tirant parti de la mise en cache de sortie. Vous découvrez comment mettre en cache le résultat retourné à partir d’une action de contrôleur afin que le même contenu n’aient pas à créer chaque fois qu'un nouvel utilisateur appelle l’action.


L’objectif de ce didacticiel est d’expliquer comment vous pouvez améliorer considérablement les performances d’une application ASP.NET MVC en tirant parti du cache de sortie. Le cache de sortie vous permet de mettre en cache le contenu retourné par une action de contrôleur. De cette façon, le même contenu n’a pas besoin être généré à chaque fois que la même action de contrôleur est appelée.

Par exemple, imaginez que votre application ASP.NET MVC affiche une liste d’enregistrements de base de données dans une vue nommée Index. Normalement, chaque fois que l’utilisateur appelle l’action du contrôleur qui retourne la vue Index, de l’ensemble d’enregistrements de base de données doit être récupérée à partir de la base de données en exécutant une requête de base de données.

Si, en revanche, vous bénéficiez du cache de sortie vous pouvez éviter l’exécution d’une requête de base de données chaque fois qu’un utilisateur appelle la même action de contrôleur. La vue peut être récupérée à partir du cache au lieu de l’action du contrôleur en cours de régénération. Active la mise en cache vous permet d’éviter l’exécution redondant fonctionnent sur le serveur.

#### <a name="enabling-output-caching"></a>L’activation de la mise en cache de sortie

Activer la mise en cache de sortie en ajoutant un &lt;OutputCache&gt; à une action de contrôleur individuels ou d’une classe de contrôleur ensemble d’attributs. Par exemple, le contrôleur dans la liste 1 expose une action nommée Index(). La sortie de l’action Index() est mis en cache pendant 10 secondes.

**La liste 1 – Controllers\HomeController.vb**

[!code-vb[Main](improving-performance-with-output-caching-vb/samples/sample1.vb)]


Dans les versions bêta d’ASP.NET MVC, la mise en cache de sortie ne fonctionne pas pour une URL telle que [http://www.MySite.com/](http://www.mysite.com/). Au lieu de cela, vous devez entrer une URL telle que [http://www.MySite.com/Home/Index](http://www.mysite.com/Home/Index).


Dans la liste de 1, la sortie de l’action Index() est mis en cache pendant 10 secondes. Si vous préférez, vous pouvez spécifier une durée beaucoup plus longue. Par exemple, si vous souhaitez mettre en cache la sortie d’une action de contrôleur pour un jour vous pouvez spécifier une durée de cache de 86 400 secondes (60 secondes \* 60 minutes \* 24 heures).

Il existe aucune garantie que le contenu ne sera mis en cache pendant la durée que vous spécifiez. Lorsque les ressources mémoire sont basses, le cache démarre automatiquement l’éviction du contenu.

Le contrôleur Home dans la liste 1 retourne la vue de l’Index dans la liste 2. Il n’a rien de spécial sur cette vue. La vue Index affiche simplement l’heure actuelle (voir Figure 1).

**Listing 2 – Views\Home\Index.aspx**

[!code-aspx[Main](improving-performance-with-output-caching-vb/samples/sample2.aspx)]

**Figure 1 : vue d’Index de la mise en cache**

![clip_image002](improving-performance-with-output-caching-vb/_static/image1.jpg)

Si vous appelez l’action Index() plusieurs fois en entrant l’URL /Home/Index dans la barre d’adresses de votre navigateur et en appuyant sur le bouton Actualiser/recharger dans votre navigateur à plusieurs reprises, puis l’heure de l’affichage de l’Index ne changera pas pendant 10 secondes. Le même temps s’affiche, car la vue est mis en cache.

Il est important de comprendre que la vue est mis en cache pour tous les utilisateurs visitant votre application. Toute personne qui appelle l’action Index() obtiendrez la même version de mise en cache de la vue Index. Cela signifie que la quantité de travail que le serveur web doit effectuer pour prendre en charge l’affichage de l’Index est considérablement réduite.

La vue dans la liste 2 se produit pour faire quelque chose de simple. La vue affiche uniquement l’heure actuelle. Toutefois, vous pouvez tout aussi facilement cache une vue qui affiche un ensemble d’enregistrements de base de données. Dans ce cas, le jeu d’enregistrements de base de données ne doit pas à récupérer à partir de la base de données chaque fois que l’action du contrôleur qui retourne la vue est appelée. La mise en cache peut réduire la quantité de travail que votre serveur web et le serveur de base de données doivent effectuer.

N’utilisez pas la page &lt;% @ OutputCache %&gt; directive dans une vue MVC. Cette directive est traces sur les stocks de Web Forms et ne doit pas être utilisée dans une application ASP.NET MVC. 

#### <a name="where-content-is-cached"></a>Où le contenu est mis en cache

Par défaut, lorsque vous utilisez la &lt;OutputCache&gt; attribut, le contenu est mis en cache dans trois emplacements : le serveur web, tous les serveurs proxy et le navigateur web. Vous pouvez contrôler exactement où le contenu est mis en cache en modifiant la propriété d’emplacement de la &lt;OutputCache&gt; attribut.

Vous pouvez définir la propriété d’emplacement à l’une des valeurs suivantes :

> · N’importe quel
> 
> · Client
> 
> · En aval
> 
> · Serveur
> 
> · Aucun
> 
> · ServerAndClient


Par défaut, la propriété d’emplacement a la valeur de tout. Toutefois, il existe dans laquelle vous pouvez être amené à mettre en cache uniquement sur le navigateur ou uniquement sur le serveur. Par exemple, si vous mettez en cache d’informations personnalisé pour chaque utilisateur vous ne convient pas de mettre en cache les informations sur le serveur. Si vous affichez des informations différentes à différents utilisateurs puis vous devez mettre en cache les informations uniquement sur le client.

Par exemple, le contrôleur dans la liste 3 expose une action nommée GetName() qui renvoie le nom d’utilisateur actuel. Si le connecteur se connecte au site Web et appelle l’action GetName() l’action retourne la chaîne « Hi prise ». Si, par la suite, Jill se connecte au site Web et appelle l’action GetName() puis elle également obtenez la chaîne « Hi prise ». La chaîne est mis en cache sur le serveur web pour tous les utilisateurs après que prise appelle initialement l’action du contrôleur.

**La liste 3 – Controllers\BadUserController.vb**

[!code-vb[Main](improving-performance-with-output-caching-vb/samples/sample3.vb)]

Très probablement, le contrôleur dans la liste 3 ne fonctionne pas comme vous le souhaitez. Vous ne souhaitez pas afficher le message « Hi prise » pour Jill.

Vous devez jamais mettre en cache un contenu personnalisé dans le cache du serveur. Toutefois, vous pouvez souhaiter mettre en cache le contenu personnalisé dans le cache du navigateur pour améliorer les performances. Si vous mettez en cache le contenu dans le navigateur, et un utilisateur appelle l’action du contrôleur même plusieurs fois, le contenu peut être récupéré à partir du cache du navigateur au lieu du serveur.

Le contrôleur modifié dans la liste 4 met en cache la sortie de l’action GetName(). Toutefois, le contenu est mis en cache uniquement sur le navigateur et non sur le serveur. Ainsi, lorsque plusieurs utilisateurs appeler la méthode GetName(), chacun obtient son propre nom d’utilisateur et le nom d’utilisateur pas à une autre personne.

**La liste 4 – Controllers\UserController.vb**

[!code-vb[Main](improving-performance-with-output-caching-vb/samples/sample4.vb)]

Notez que la &lt;OutputCache&gt; attribut dans la liste 4 inclut une propriété d’emplacement définie sur la valeur OutputCacheLocation.Client. Le &lt;OutputCache&gt; attribut inclut également une propriété NoStore. La propriété NoStore est utilisée pour informer les navigateurs et les serveurs proxy qu’ils ne doivent pas stocker une copie permanente du contenu mis en cache.

#### <a name="varying-the-output-cache"></a>Faisant varier le Cache de sortie

Dans certaines situations, vous pourriez différentes versions de mise en cache de contenu très semblable. Par exemple, imaginez que vous créez une page maître/détail. La page maître affiche une liste de titres de films. Lorsque vous cliquez sur un titre, vous obtenez les détails de la séquence sélectionnée.

Si vous mettez en cache la page de détails, puis les détails pour un même film seront affichera, quel que soit le film que vous cliquez sur. Le premier film sélectionné par le premier utilisateur s’affichera à tous les utilisateurs de futures.

Vous pouvez résoudre ce problème en tirant parti de la propriété VaryByParam de la &lt;OutputCache&gt; attribut. Cette propriété vous permet de créer différentes versions de mise en cache du contenu même lorsqu’un paramètre de formulaire ou varie en fonction du paramètre de chaîne de requête.

Par exemple, le contrôleur dans la liste 5 expose deux actions nommées Master() et Details(). L’action Master() retourne une liste de titres de films et de l’action Details() renvoie les détails de la séquence sélectionnée.

**La liste 5 – Controllers\MoviesController.vb**

[!code-vb[Main](improving-performance-with-output-caching-vb/samples/sample5.vb)]

L’action Master() inclut une propriété VaryByParam avec la valeur « none ». Lorsque le Master() action est appelée, la même version de mise en cache du maître d’affichage est retourné. Les paramètres de l’écran ou la chaîne de requête les paramètres sont ignorés (voir Figure 2).

**Figure 2 : la vue /Movies/Master**

![clip_image004](improving-performance-with-output-caching-vb/_static/image2.jpg)

**Figure 3 : l’affichage des détails/films**

![clip_image006](improving-performance-with-output-caching-vb/_static/image3.jpg)

L’action Details() inclut une propriété VaryByParam avec la valeur « Id ». Lorsque des valeurs différentes pour le paramètre Id sont passés à l’action du contrôleur, différentes versions de mise en cache du mode Détails sont générées.

Il est important de comprendre qu’à l’aide de la propriété VaryByParam entraîne la mise en cache plus et moins. Une autre version de mise en cache du mode Détails est créée pour chaque version différente du paramètre Id.

Vous pouvez définir la propriété VaryByParam les valeurs suivantes :

> \*= Créer une autre version de mise en cache chaque fois qu’un paramètre de chaîne de requête ou de formulaire varie.
> 
> None = jamais créer différentes versions de mise en cache
> 
> Liste des points-virgules de paramètres = créer différentes versions de mise en cache chaque fois que les paramètres de chaîne de requête ou de formulaire dans la liste de varie


#### <a name="creating-a-cache-profile"></a>Création d’un profil de Cache

En guise d’alternative à la configuration des propriétés de cache de sortie en modifiant les propriétés de la &lt;OutputCache&gt; attribut, vous pouvez créer un profil de cache dans le fichier de configuration (web.config) web. Création d’un profil de cache dans le fichier de configuration web offre plusieurs avantages importants.

Tout d’abord, en configurant la sortie mise en cache dans le fichier de configuration web, vous pouvez contrôler comment le contenu dans un emplacement central du cache des actions de contrôleur. Vous pouvez créer un profil de cache et appliquer le profil à plusieurs contrôleurs ou les actions de contrôleur.

Ensuite, vous pouvez modifier le fichier de configuration web sans avoir à recompiler votre application. Si vous devez désactiver la mise en cache pour une application qui a déjà été déployée en production, vous pouvez simplement modifier les profils de cache définis dans le fichier de configuration web. Toutes les modifications au fichier de configuration web seront détectées automatiquement et appliquées.

Par exemple, le &lt;mise en cache&gt; section de configuration web dans la liste 6 définit un profil de cache nommé Cache1Hour. Le &lt;mise en cache&gt; section doit apparaître dans le &lt;system.web&gt; section d’un fichier de configuration web.

**La liste 6 – section mise en cache pour le fichier web.config**

[!code-xml[Main](improving-performance-with-output-caching-vb/samples/sample6.xml)]

Le contrôleur dans la liste 7 illustre comment vous pouvez appliquer le profil Cache1Hour à une action de contrôleur avec le &lt;OutputCache&gt; attribut.

**La liste 7 – Controllers\ProfileController.vb**

[!code-vb[Main](improving-performance-with-output-caching-vb/samples/sample7.vb)]

Si vous appelez l’action Index() exposée par le contrôleur dans la liste 7, le même temps sera retourné pour 1 heure.

#### <a name="summary"></a>Résumé

La mise en cache de sortie vous offre une méthode très simple qui améliore considérablement les performances de vos applications ASP.NET MVC. Dans ce didacticiel, vous avez appris à utiliser le &lt;OutputCache&gt; attribut à mettre en cache la sortie d’actions de contrôleur. Vous avez également appris à modifier les propriétés de la &lt;OutputCache&gt; attribut telles que les propriétés Duration et VaryByParam pour modifier la façon dont le contenu obtient mis en cache. Enfin, vous avez appris à définir des profils de cache dans le fichier de configuration web.

>[!div class="step-by-step"]
[Précédent](understanding-action-filters-vb.md)
[Suivant](adding-dynamic-content-to-a-cached-page-vb.md)
