---
title: Affichage des composants
author: rick-anderson
description: "Affichage des composants visent n’importe où que la logique de rendu réutilisables."
keywords: ASP.NET Core, affichage des composants, vue partielle
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: ab4705b7-59d7-4f31-bc97-ea7f292fe926
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-components
ms.openlocfilehash: 3bc6d3f85d8ea7fb9b72b18cfd9c5ec2d07293b0
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2017
---
# <a name="view-components"></a>Affichage des composants

De [Rick Anderson](https://twitter.com/RickAndMSFT)

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))

## <a name="introducing-view-components"></a>Présentation des composants de la vue

Nouveau vers ASP.NET MVC de base, affichage des composants sont similaires aux vues partielles, mais elles sont beaucoup plus puissantes. Affichage des composants n’utiliser la liaison de modèle et dépendre uniquement les données que vous fournissez lors de l’appel dans celui-ci. Un composant de vue :

* Restitue un segment au lieu d’une réponse complète
* Inclut le mêmes séparation des intérêts et les avantages de testabilité trouvées entre un contrôleur et une vue
* Peut avoir des paramètres et la logique métier
* Est généralement appelé à partir d’une page de disposition

Affichage des composants sont destinées n’importe où que vous avez une logique de rendu réutilisables qui est trop complexe pour une vue partielle, telles que :

* Menus de navigation dynamique
* Nuage de balises (où elle interroge la base de données)
* Panneau de configuration de connexion
* Panier d’achat
* Articles publiés récemment
* Contenu de la barre latérale sur un blog classique
* Un panneau de connexion qui est rendu sur chaque page et affiche les liens pour déconnecter ou de journal, selon le journal dans l’état de l’utilisateur

Un composant de vue se compose de deux parties : la classe (généralement dérivé [ViewComponent](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponent)) et le résultat renvoie (en général, une vue). Tels que les contrôleurs, un composant de vue peut être un POCO, mais il est que la plupart des développeurs tirer parti des méthodes et propriétés disponibles en dérivant de `ViewComponent`.

## <a name="creating-a-view-component"></a>Création d’un composant de vue

Cette section contient les exigences d’ordre général pour créer un composant de vue. Plus loin dans l’article, nous examiner chaque étape en détail et créer un composant de vue.

### <a name="the-view-component-class"></a>La classe de composant de vue

Une classe de composant de vue peut être créée à l’aide d’une des opérations suivantes :

* Dérivation de *ViewComponent*
* Le fait de décorer une classe avec le `[ViewComponent]` attribut ou la dérivation d’une classe avec le `[ViewComponent]` attribut
* Création d’une classe dans laquelle le nom se termine par le suffixe *ViewComponent*

Comme contrôleurs, affichage des composants doivent être des classes publiques, non imbriquée et non abstraite. Le nom de composant de vue est le nom de classe avec le suffixe « ViewComponent » supprimé. Il peut également être explicitement spécifié à l’aide de la `ViewComponentAttribute.Name` propriété.

Une classe de composant de vue :

* Prend entièrement en charge le constructeur [injection de dépendance](../../fundamentals/dependency-injection.md)

* Ne font pas partie du cycle de vie du contrôleur, ce qui signifie que vous ne pouvez pas utiliser [filtres](../controllers/filters.md) dans un composant de vue

### <a name="view-component-methods"></a>Afficher les méthodes de composant

Un composant de vue définit sa logique dans un `InvokeAsync` méthode qui retourne un `IViewComponentResult`. Paramètres proviennent directement d’appel du composant de vue, pas à partir de la liaison de modèle. Un composant de vue gère jamais directement une requête. En règle générale, un composant de vue Initialise un modèle et le passe à une vue en appelant le `View` (méthode). En résumé, afficher les méthodes du composant :

* Définir un `InvokeAsync` méthode qui retourne un`IViewComponentResult`
* En général, initialise un modèle et le passe à une vue en appelant le `ViewComponent` `View` (méthode)
* Paramètres proviennent de la méthode d’appel, pas HTTP, il n’existe aucune liaison de modèle
* Sont n’est pas accessible directement en tant que point de terminaison HTTP, ils sont appelés à partir de votre code (généralement dans une vue). Un composant de vue gère jamais une demande
* Sont surchargées sur la signature, plutôt que des détails dans la requête HTTP actuelle

### <a name="view-search-path"></a>Chemin de recherche de vue

Le runtime recherche dans la vue dans les chemins d’accès suivants :

   * Vues /\<controller_name > /Components/\<view_component_name > /\<view_name >
   * Vues/Shared/Components/\<view_component_name > /\<view_name >

Le nom de la vue par défaut pour un composant de vue est *par défaut*, ce qui signifie que votre fichier d’affichage est généralement appelé *Default.cshtml*. Vous pouvez spécifier un nom d’affichage différent lors de la création du résultat de composant de vue ou lorsque vous appelez le `View` (méthode).

Nous vous recommandons de vous nommez le fichier de vue *Default.cshtml* et utiliser le *vues/Shared/Components/\<view_component_name > /\<view_name >* chemin d’accès. Le `PriorityList` composant de la vue utilisée dans cet exemple utilise *Views/Shared/Components/PriorityList/Default.cshtml* pour l’affichage du composant.

## <a name="invoking-a-view-component"></a>Appel d’un composant de vue

Pour utiliser le composant d’affichage, appelez ce qui suit à l’intérieur d’une vue :

```html
@Component.InvokeAsync("Name of view component", <anonymous type containing parameters>)
```

Les paramètres sont transmis à la `InvokeAsync` (méthode). Le `PriorityList` développé dans l’article de composant de vue est appelée à partir de la *Views/Todo/Index.cshtml* afficher le fichier. Dans l’exemple suivant, la `InvokeAsync` méthode est appelée avec deux paramètres :

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

## <a name="invoking-a-view-component-as-a-tag-helper"></a>Appel d’un composant de vue comme une application d’assistance de balise

Pour ASP.NET Core 1.1 et versions ultérieures, vous pouvez appeler un composant de la vue comme une [application d’assistance de balise](xref:mvc/views/tag-helpers/intro):

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

Paramètres de classe et méthode casse Pascal pour les programmes d’assistance de balise sont traduites en leur [réduire les cas rapide](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101). L’application d’assistance de balise pour appeler un composant de vue utilise le `<vc></vc>` élément. Le composant d’affichage est spécifié comme suit :

```html
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

Remarque : Pour pouvoir utiliser un composant de vue comme une application d’assistance de balise, vous devez inscrire l’assembly contenant le composant de vue à l’aide de la `@addTagHelper` la directive. Par exemple, si votre composant de la vue se trouve dans un assembly appelé « MyWebApp », ajoutez la directive suivante à la `_ViewImports.cshtml` fichier :

```csharp
@addTagHelper *, MyWebApp
```

Vous pouvez inscrire un composant de vue comme une application d’assistance de balise à n’importe quel fichier qui fait référence au composant de la vue. Consultez [la gestion de balise d’assistance étendue](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) pour plus d’informations sur l’inscription des programmes d’assistance de balise.

Le `InvokeAsync` méthode utilisée dans ce didacticiel :

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

Dans le balisage de l’application d’assistance de balise :

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

Dans l’exemple ci-dessus, le `PriorityList` composant de vue devient `priority-list`. Les paramètres pour le composant de vue sont passés en tant qu’attributs en minuscules rapide.

### <a name="invoking-a-view-component-directly-from-a-controller"></a>Appel d’un composant de vue directement à partir d’un contrôleur

Affichage des composants sont généralement appelées à partir d’une vue, mais vous pouvez les appeler directement à partir d’une méthode de contrôleur. Lors de l’affichage des composants ne définissent pas de points de terminaison tels que les contrôleurs, vous pouvez facilement implémenter une action du contrôleur qui retourne le contenu d’un `ViewComponentResult`.

Dans cet exemple, le composant de vue est appelé directement à partir du contrôleur :

[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a>Procédure pas à pas : Création d’un composant de la vue simple

[Télécharger](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), générer et tester le code de démarrage. Il s’agit d’un projet simple avec un `Todo` contrôleur qui affiche une liste de *Todo* éléments.

![Liste des ToDos](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a>Ajoutez une classe ViewComponent

Créer un *ViewComponents* dossier et ajoutez le code suivant `PriorityListViewComponent` classe :

[!code-csharp[Main](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

Remarques sur le code :

* Classes de composant de vue peuvent être contenus dans **tout** dossier dans le projet.
* Étant donné que le nom de classe PriorityList**ViewComponent** se termine par le suffixe **ViewComponent**, le runtime utilise la chaîne « PriorityList » lors du référencement du composant de la classe à partir d’une vue. Je vais expliquer que plus en détail ultérieurement.
* Le `[ViewComponent]` attribut peut modifier le nom utilisé pour faire référence à un composant de vue. Par exemple, nous pourrions avoir nommé la classe `XYZ` et appliqué les `ViewComponent` attribut :

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* Le `[ViewComponent]` attribut ci-dessus indique le sélecteur de composants de vue d’utiliser le nom `PriorityList` lors de la recherche pour les vues associées au composant et à utiliser la chaîne « PriorityList » lors du référencement du composant de la classe à partir d’une vue. Je vais expliquer que plus en détail ultérieurement.
* Le composant utilise [injection de dépendance](../../fundamentals/dependency-injection.md) afin de libérer le contexte de données.
* `InvokeAsync`expose une méthode qui peut être appelée à partir d’une vue et peut prendre un nombre arbitraire d’arguments.
* Le `InvokeAsync` méthode retourne le jeu de `ToDo` éléments qui répondent à la `isDone` et `maxPriority` paramètres.

### <a name="create-the-view-component-razor-view"></a>Créer la vue Razor vue du composant

* Créer le *composants/Shared/vues* dossier. Ce dossier **doit** nommé *composants*.

* Créer le *vues/Shared/composants/PriorityList* dossier. Ce nom de dossier doit correspondre à celui de la classe de composant de vue, ou le nom de la classe moins le suffixe (si nous avons suivi convention et utilisé la *ViewComponent* suffixe dans le nom de classe). Si vous avez utilisé le `ViewComponent` attribut, le nom de classe doit correspondre à la désignation de l’attribut.

* Créer un *Views/Shared/Components/PriorityList/Default.cshtml* vue Razor :[!code-html[Main](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]
    
   La vue Razor utilise une liste de `TodoItem` et les affiche. Si le composant de vue `InvokeAsync` méthode ne passe pas le nom de la vue (comme dans notre exemple), *par défaut* est utilisé pour le nom de la vue par convention. Plus loin dans ce didacticiel, je vous montrerai comment passer le nom de la vue. Pour remplacer le style par défaut pour un contrôleur spécifique, ajoutez une vue dans le dossier d’affichage propres au contrôleur (par exemple *Views/Todo/Components/PriorityList/Default.cshtml)*.
    
    Si le composant de la vue est spécifique du contrôleur, vous pouvez l’ajouter au dossier spécifique du contrôleur (*Views/Todo/Components/PriorityList/Default.cshtml*).

* Ajouter un `div` contenant un appel au composant de liste de priorité vers le bas de la *Views/Todo/index.cshtml* fichier :

    [!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFirst.cshtml?range=34-38)]

Le balisage `@await Component.InvokeAsync` illustre la syntaxe d’appel de composants de la vue. Le premier argument est le nom du composant que vous souhaitez appeler ou d’appeler. Les paramètres suivants sont passés au composant. `InvokeAsync`peut prendre un nombre arbitraire d’arguments.

Tester l’application. L’illustration suivante montre la liste de tâches et les éléments de priorité :

![éléments de liste et la priorité de tâche](view-components/_static/pi.png)

Vous pouvez également appeler le composant de vue directement à partir du contrôleur :

[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![éléments de priorité à partir de l’action de IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a>En spécifiant un nom de vue

Un composant de vue complexe devrez peut-être spécifier une vue par défaut dans certaines conditions. Le code suivant montre comment spécifier la vue « PVC » à partir de la `InvokeAsync` (méthode). Mise à jour la `InvokeAsync` méthode dans la `PriorityListViewComponent` classe.

[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

Copie le *Views/Shared/Components/PriorityList/Default.cshtml* fichier à une vue nommée *Views/Shared/Components/PriorityList/PVC.cshtml*. Ajouter un titre pour indiquer que la vue PVC est utilisée.

[!code-html[Main](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

Mise à jour *Views/TodoList/Index.cshtml*:

<!-- Views/TodoList/Index.cshtml is never imported, so change to test tutorial -->

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

Exécutez l’application et vérifier l’affichage de PVC.

![Composant de vue de priorité](view-components/_static/pvc.png)

Si la vue PVC n’est pas affichée, vérifiez que vous appelez le composant de vue avec une priorité de 4 ou version ultérieure.

### <a name="examine-the-view-path"></a>Examinez le chemin d’accès de la vue

* Modifiez le paramètre de priorité inférieure ou égale à trois afin de la vue priority n’est pas renvoyée.
* Renommez temporairement le *Views/Todo/Components/PriorityList/Default.cshtml* à *1Default.cshtml*.
* Tester l’application, vous obtiendrez l’erreur suivante :

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' was not found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* Copie *Views/Todo/Components/PriorityList/1Default.cshtml* à *Views/Shared/Components/PriorityList/Default.cshtml*.
* Ajouter des balises à la *Shared* provient de la vue du composant Todo vue pour indiquer la vue de la *Shared* dossier.
* Test du **Shared** vue du composant.

![Sortie de tâches avec la vue du composant partagé](view-components/_static/shared.png)

### <a name="avoiding-magic-strings"></a>Éviter les chaînes magiques

Si vous voulez compiler la sécurité, vous pouvez remplacer le nom du composant codé en dur la vue par le nom de classe. Créer le composant de vue sans le suffixe « ViewComponent » :

[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

Ajouter un `using` instruction pour votre Razor afficher le fichier et utiliser le `nameof` opérateur :

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexNameof.cshtml?range=1-6,33-)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Injection de dépendances dans les vues](dependency-injection.md)
