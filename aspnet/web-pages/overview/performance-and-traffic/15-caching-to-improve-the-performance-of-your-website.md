---
uid: web-pages/overview/performance-and-traffic/15-caching-to-improve-the-performance-of-your-website
title: "La mise en cache des données dans une application Web Pages (Razor) Site pour de meilleures performances | Documents Microsoft"
author: tfitzmac
description: "Vous pouvez accélérer votre site Web en le faisant magasin : autrement dit, cache - les résultats des données qui habituellement prendrait beaucoup de temps pour récupérer ou traiter un..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/14/2014
ms.topic: article
ms.assetid: 961e525b-7700-469e-8a68-d7010b6fb68c
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/performance-and-traffic/15-caching-to-improve-the-performance-of-your-website
msc.type: authoredcontent
ms.openlocfilehash: c747fef33a6d1db19f09fd0303c47d689b956687
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="caching-data-in-an-aspnet-web-pages-razor-site-for-better-performance"></a>Mise en cache des données dans un Site de Pages (Razor) Web ASP.NET pour de meilleures performances
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article explique comment utiliser un programme d’assistance en cache les informations pour améliorer les performances dans un site Web ASP.NET Web Pages (Razor). Vous pouvez accélérer votre site Web en le faisant magasin &#8212; Autrement dit, le cache &#8212; les résultats des données qui habituellement prendrait beaucoup de temps pour récupérer ou traiter et qui ne changent pas souvent.
> 
> **Ce que vous allez apprendre :** 
> 
> - L’utilisation de la mise en cache pour améliorer la réactivité de votre site Web.
> 
> Voici les fonctionnalités d’ASP.NET introduites dans l’article :
> 
> - Le `WebCache` helper.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 3
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2.


Chaque fois qu’un utilisateur demande une page de votre site, le serveur web doit effectuer un travail pour répondre à la demande. Pour certaines de vos pages, le serveur peut avoir effectuer des tâches qui prennent (comparativement) beaucoup de temps, telles que la récupération des données à partir d’une base de données. Même si ces tâches ne prennent longtemps en termes absolus, si votre site rencontre un trafic important, une série de requêtes individuelles que le serveur web effectuer la tâche complexe ou lente risque peut s’ajouter une grande quantité de travail. Cela peut finalement affecter les performances du site.

Permet d’améliorer les performances de votre site Web dans des circonstances, comme cela est en cache des données. Si votre site Obtient les demandes répétées pour les mêmes informations, les informations ne doivent pas être modifiés pour chaque personne, et il n’est pas temps sensibles, au lieu de nouveau l’extraction ou le recalcul, vous pouvez extraire les données une seule fois et puis stocker les résultats. La prochaine fois qu’une demande arrive pour que plus d’informations, vous laisser l’hors du cache.

En règle générale, vous mettez en cache les informations qui ne changent pas fréquemment. Lorsque vous placez les informations dans le cache, il est stocké en mémoire sur le serveur web. Vous pouvez spécifier la durée pendant laquelle il doit être mis en cache, de quelques secondes à jours. Lors de la période de mise en cache expire, les informations sont supprimées automatiquement à partir du cache.

> [!NOTE]
> Entrées dans le cache peuvent être supprimées pour des raisons autres que qu’ils ont expiré. Par exemple, le serveur web peut temporairement manquer de mémoire et une façon qu’il puisse récupérer la mémoire consiste à lever les entrées du cache. Comme vous le verrez, même si vous avez placer les informations dans le cache, vous devez vérifier pour vous assurer qu’il est toujours active lorsque vous avez besoin.


Imaginez que votre site Web dispose d’une page qui affiche la température actuelle et les prévisions météorologiques. Pour obtenir ce type d’informations, vous pouvez envoyer une demande à un service externe. Étant donné que ces informations ne changent pas beaucoup (dans un délai de deux heures, par exemple) et les appels externes nécessitant une heure et la bande passante, il est un bon candidat pour la mise en cache.

## <a name="adding-caching-to-a-page"></a>Ajout de la mise en cache à une Page

ASP.NET inclut un `WebCache` assistance qui facilite la tâche Ajouter la mise en cache à votre site et ajouter des données dans le cache. Dans cette procédure, vous allez créer une page qui met en cache l’heure actuelle. Cela n’est pas un exemple concret, étant donné que l’heure actuelle est un élément qui ne changent pas souvent et qui n’est plus complexe à calculer. Toutefois, il est un bon moyen pour illustrer l’action de mise en cache.

1. Ajouter une nouvelle page nommée *WebCache.cshtml* au site Web.
2. Ajoutez le code et le balisage suivant à la page :

    [!code-cshtml[Main](15-caching-to-improve-the-performance-of-your-website/samples/sample1.cshtml)]

    Lors de la mise en cache des données, vous placez dans le cache à l’aide d’un nom de cela est unique sur le site Web. Dans ce cas, vous allez utiliser une entrée de cache nommée `CachedTime`. Il s’agit de la `cacheItemKey` indiqué dans l’exemple de code.

    Le code lit d’abord le `CachedTime` l’entrée de cache. Si une valeur est retournée (autrement dit, si l’entrée de cache n’est pas null), le code définit uniquement la valeur de la variable de temps pour les données du cache.

    Toutefois, si l’entrée de cache n’existe pas (autrement dit, il est null), le code définit la valeur d’heure, il ajoute au cache et définit une valeur d’expiration d’une minute. Après une minute, l’entrée du cache est ignorée. (La valeur d’expiration par défaut pour un élément dans le cache est de 20 minutes). La commande `WebCache.Set(cacheItemKey, time, 1, false)` montre comment ajouter la valeur de temps actuelle dans le cache et définir son expiration à 1 minute. Définition de la *slidingExpiration* paramètre `false` signifie que le délai d’expiration n’est pas renouvelé chaque fois qu’elle est demandée. Sa date d’expiration exactement 1 minute après que qu’il a été ajoutée au cache. Si vous définissez cette valeur sur `true` le délai d’expiration de 1 minute est réinitialisé chaque fois que la valeur est demandée à partir du cache.

    Ce code illustre le modèle que vous devez toujours utiliser lorsque le cache de données. Avant d’obtenir quelque chose hors du cache, commencez toujours par vérifier si le `WebCache.Get` méthode a retourné une valeur null. Souvenez-vous que l’entrée de cache a peut-être expiré ou a peut-être été supprimée pour une raison quelconque, donc toute entrée donnée n’est jamais garantie dans le cache.
3. Exécutez *WebCache.cshtml* dans un navigateur. (Assurez-vous que la page est sélectionnée dans le **fichiers** espace de travail avant de l’exécuter.) La première fois que vous demandez la page, les données d’heure n’est pas dans le cache et le code doit ajouter la valeur d’heure dans le cache.

    ![cache-1](15-caching-to-improve-the-performance-of-your-website/_static/image1.jpg)
4. Actualiser *WebCache.cshtml* dans le navigateur. Cette fois, les données de temps sont dans le cache. Notez que l’heure n’a pas changé depuis la dernière fois que vous avez consulté la page.

    ![cache-2](15-caching-to-improve-the-performance-of-your-website/_static/image2.jpg)
5. Patientez une minute de vider le cache, puis actualisez la page. La page indique à nouveau que les données de temps n’a pas été trouvées dans le cache, et l’heure de mise à jour est ajouté au cache.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires


- [Affichage des données dans un graphique](https://go.microsoft.com/fwlink/?LinkId=202895)
- [Référence de l’API de WebCache](https://msdn.microsoft.com/en-us/library/system.web.helpers.webcache(v=vs.99).aspx) (MSDN)
