---
title: "Injection de dépendance dans les vues"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 80fb9e43-e4db-4af2-b2a8-e1364a712f69
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/dependency-injection
ms.openlocfilehash: 4586f50bc663b7269914dfff28b61342e3991a48
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="dependency-injection-into-views"></a>Injection de dépendance dans les vues

Par [Steve Smith](https://ardalis.com/)

Prend en charge par ASP.NET Core [injection de dépendance](xref:fundamentals/dependency-injection) dans les vues. Cela peut être utile pour les services spécifiques à la vue, telles que la localisation ou les données requises pour le remplissage d’éléments de la vue. Vous devez tenter de mettre à jour [séparation des préoccupations](http://deviq.com/separation-of-concerns/) entre vos contrôleurs et les vues. La plupart de vos affichages de données doit être passée à partir du contrôleur.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="a-simple-example"></a>Un exemple Simple

Vous pouvez injecter un service dans une vue à l’aide de la `@inject` la directive. Vous pouvez considérer `@inject` en tant que l’ajout d’une propriété à votre affichage et pour renseigner la propriété à l’aide de DI.

La syntaxe de `@inject`:`@inject <type> <name>`

Un exemple de `@inject` en action :

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

Cette vue affiche une liste de `ToDoItem` instances, ainsi qu’un résumé montrant les statistiques globales. Le résumé est rempli à partir du texte injecté `StatisticsService`. Ce service est inscrit pour l’injection de dépendance dans `ConfigureServices` dans *Startup.cs*:

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

Le `StatisticsService` effectue des calculs sur l’ensemble de `ToDoItem` instances, auquel elle accède via un référentiel :

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,26)]

Le référentiel de l’exemple utilise une collection en mémoire. L’implémentation ci-dessus (qui fonctionne sur toutes les données en mémoire) n’est pas recommandée pour les jeux de données volumineux, accessible à distance.

L’exemple affiche les données du modèle lié à la vue et le service injectés dans la vue :

![Pour afficher la liste des éléments de total, terminer des éléments de priorité moyenne et une liste de tâches avec leurs niveaux de priorité et les valeurs booléennes indiquant l’achèvement.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a>Remplissage des données de recherche

Injection de la vue peut être utile pour remplir les options dans les éléments d’interface utilisateur, telles que des listes déroulantes. Envisagez d’un formulaire de profil utilisateur qui inclut des options permettant de spécifier le sexe, l’état et autres préférences. Rendu d’une forme à l’aide d’une approche MVC standard nécessiterait le contrôleur pour demander des services d’accès aux données pour chacun de ces jeux d’options et puis remplir un modèle ou `ViewBag` avec chaque ensemble d’options pour être lié.

Une autre approche injecte services directement dans la vue pour obtenir les options. Cela réduit la quantité de code requis par le contrôleur, le déplacement de cette logique de construction d’élément vue dans la vue proprement dite. L’action du contrôleur pour afficher un formulaire de modification de profil ne doit passer l’instance de profil :

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

Le formulaire HTML utilisé pour mettre à jour ces préférences inclut les listes déroulantes pour trois propriétés :

![Mettre à jour la vue profil avec un formulaire permettant l’entrée de nom, sexe, état et couleur préférée.](dependency-injection/_static/updateprofile.png)

Ces listes sont remplies par un service qui a été injecté dans la vue :

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

Le `ProfileOptionsService` est un service au niveau de l’interface utilisateur conçu pour fournir les données nécessaires pour ce formulaire :

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

>[!TIP]
> N’oubliez pas d’enregistrer les types de demande par le biais d’injection de dépendance dans le `ConfigureServices` méthode dans *Startup.cs*.

## <a name="overriding-services"></a>Substitution des Services

En plus de l’injection de nouveaux services, cette technique peut également être utilisée pour remplacer des services injectées précédemment sur une page. La figure ci-dessous montre tous les champs disponibles dans la page utilisée dans le premier exemple :

![Menu contextuel d’IntelliSense sur typé qui répertorie les champs Html, composant, StatsService et Url de symbole @](dependency-injection/_static/razor-fields.png)

Comme vous pouvez le voir, les champs par défaut incluent `Html`, `Component`, et `Url` (ainsi que le `StatsService` qui nous injectées). Si vous souhaitez par exemple remplacer les programmes d’assistance HTML de valeur par défaut par les vôtres, facilement procéder à l’aide de `@inject`:

[!code-html[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

Si vous souhaitez étendre les services existants, vous pouvez simplement utiliser cette technique lors héritant ou encapsulant l’implémentation existante par les vôtres.

## <a name="see-also"></a>Voir aussi

* Blog de Simon Timms : [mise en route de rechercher des données dans votre vue](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)
