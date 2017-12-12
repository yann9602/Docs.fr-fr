---
uid: single-page-application/overview/introduction/knockoutjs-template
title: "Application à Page unique : Modèle KnockoutJS | Documents Microsoft"
author: MikeWasson
description: "Modèle de Knockout"
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/30/2013
ms.topic: article
ms.assetid: f9c07af0-4b20-4b08-af8f-47fc3df169a2
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /single-page-application/overview/introduction/knockoutjs-template
msc.type: authoredcontent
ms.openlocfilehash: 6e84dcc16345e33fcd3a3f83c4b35bc993c03ca6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="single-page-application-knockoutjs-template"></a>Application à Page unique : Modèle de KnockoutJS
====================
par [Mike Wasson](https://github.com/MikeWasson)

> Le modèle MVC Knockout fait partie d’ASP.NET et Web Tools 2012.2
> 
> [Télécharger ASP.NET et Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650)


La mise à jour ASP.NET et Web Tools 2012.2 inclut un modèle d’Application de Page unique (SPA) pour ASP.NET MVC 4. Ce modèle est conçu pour vous aider à démarrer rapidement à créer les applications web côté client interactives.

« Application à page unique » (SPA) est le terme général pour une application web qui charge une page HTML unique, puis met à jour la page dynamiquement, au lieu de charger les nouvelles pages. Après le chargement de page initial, SPA communique avec le serveur via les requêtes AJAX.

![](knockoutjs-template/_static/image1.png)

AJAX n’est pas nouveau, mais aujourd'hui des infrastructures JavaScript qui le rendent plus facile créer et gérer une grande application SPA sophistiquée. En outre, HTML 5 et CSS3 sont qu’il soit plus facile de créer des interfaces utilisateur riches.

Pour vous aider à démarrer, le modèle SPA crée un exemple d’application « Liste des tâches ». Dans ce didacticiel, nous allons prendre une visite guidée du modèle. Tout d’abord, nous allons examiner l’application de liste des tâches, puis examiner les éléments de la technologie qui fonctionne.

## <a name="create-a-new-spa-template-project"></a>Créez un projet de modèle SPA

Configuration requise :

- Visual Studio 2012 ou Visual Studio Express 2012 pour le Web
- Mettre à jour des outils de Web ASP.NET 2012.2. Vous pouvez installer la mise à jour [ici](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=ASPDOTNETandWebTools2012_2).

Démarrez Visual Studio et sélectionnez **nouveau projet** à partir de la page de démarrage. Ou, à partir de la **fichier** menu, sélectionnez **nouveau** , puis **projet**.

Dans le **modèles** volet, sélectionnez **modèles installés** et développez le **Visual C#** nœud. Sous **Visual C#**, sélectionnez **Web**. Dans la liste des modèles de projet, sélectionnez **ASP.NET MVC 4 Web Application**. Nommez le projet et cliquez sur **OK**.

![](knockoutjs-template/_static/image2.png)

Dans le **nouveau projet** Assistant, sélectionnez **Application à Page unique**.

![](knockoutjs-template/_static/image3.png)

Appuyez sur F5 pour générer et exécuter l'application. Lorsque l’application s’exécute tout d’abord, il affiche un écran de connexion.

![](knockoutjs-template/_static/image4.png)

Cliquez sur le &quot;inscrire&quot; lier et créer un nouvel utilisateur.

![](knockoutjs-template/_static/image5.png)

Une fois connecté, l’application crée une liste de tâches par défaut avec deux éléments. Vous pouvez cliquer sur « Ajouter une liste de tâches » pour ajouter une nouvelle liste.

![](knockoutjs-template/_static/image6.png)

Renommer la liste, ajouter des éléments à la liste et les cocher. Vous pouvez également supprimer des éléments ou supprimer une liste entière. Les modifications sont automatiquement conservées dans une base de données sur le serveur (LocalDB réellement à ce stade, étant donné que vous exécutez l’application localement).

![](knockoutjs-template/_static/image7.png)

## <a name="architecture-of-the-spa-template"></a>Architecture du modèle SPA

Ce diagramme illustre les blocs de construction principaux de l’application.

![](knockoutjs-template/_static/image8.png)

Côté serveur, ASP.NET MVC sert le code HTML et gère également l’authentification basée sur des formulaires.

API Web ASP.NET gère toutes les demandes qui se rapportent à la ToDoLists et ToDoItems, y compris la mise en route, de création, de mise à jour et de suppression. Le client échange des données avec l’API Web au format JSON.

Entity Framework (EF) est la couche de O/RM. Il sert d’intermédiaire entre le monde et orienté objet d’ASP.NET et la base de données sous-jacente. La base de données utilise la base de données locale, mais vous pouvez le modifier dans le fichier Web.config. En général, vous utilisez LocalDB pour le développement local et déployer une base de données SQL sur le serveur, à l’aide de la migration de code-first EF.

Du côté client, la bibliothèque Knockout.js gère les mises à jour de la page à partir de requêtes AJAX. Knockout utilise la liaison de données pour synchroniser la page avec les dernières données. De cette façon, il est inutile d’écrire du code qui vous guide à travers les données JSON et met à jour le modèle DOM. Au lieu de cela, vous placez des attributs déclaratifs dans le code HTML qui indiquent Knockout comment présenter les données.

Un grand avantage de cette architecture est qu’il sépare la couche de présentation de la logique d’application. Vous pouvez créer la partie de l’API Web sans connaître quoi que ce soit sur l’apparence de votre page web. Du côté client, vous créez un modèle de « vue » pour représenter ces données, et le modèle d’affichage utilise Knockout pour lier le code HTML. Qui vous permet de facilement modifier le code HTML sans modifier le modèle d’affichage. (Nous allons examiner Knockout un peu plus loin.)

## <a name="models"></a>Modèles

Dans le projet Visual Studio, le dossier de modèles contient les modèles qui sont utilisés sur le côté serveur. (Il existe également des modèles sur le côté client ; nous le verrons celles.)

![](knockoutjs-template/_static/image9.png)

**TodoItem, liste des tâches**

Il s’agit de modèles de base de données pour Entity Framework Code First. Notez que ces modèles ont des propriétés qui pointent vers les uns par rapport aux autres. `ToDoList`contient une collection de ToDoItems et chaque `ToDoItem` a une référence à son parent ToDoList. Ces propriétés sont appelées propriétés de navigation, et elles représentent la relation un-à-plusieurs, une liste de tâches et ses éléments de tâches.

Le `ToDoItem` également classe le **[ForeignKey]** attribut pour spécifier que `ToDoListId` est une clé étrangère dans la `ToDoList` table. Cela indique à EF pour ajouter une contrainte foreign key à la base de données.

[!code-csharp[Main](knockoutjs-template/samples/sample1.cs)]

**TodoItemDto, TodoListDto**

Ces classes définissent les données qui seront envoyées au client. « DTO » signifie « objet de transfert de données. » Le DTO définit comment les entités seront sérialisées en JSON. En règle générale, il existe plusieurs raisons d’utiliser DTO :

- Pour contrôler les propriétés qui sont sérialisées. Le DTO peut contenir un sous-ensemble des propriétés du modèle de domaine. Vous pouvez procéder ainsi pour des raisons de sécurité (masquer les données sensibles) ou simplement pour réduire la quantité de données que vous envoyez.
- Pour modifier la forme des données : par exemple, pour lisser une structure de données plus complexe.
- Pour conserver toute logique métier en dehors du DTO (séparation des problèmes).
- Si vos modèles de domaine ne peut pas être sérialisées pour une raison quelconque. Par exemple, les références circulaires peuvent provoquer des problèmes lorsque vous sérialisez un objet sont moyens de gérer ce problème dans l’API Web (consultez [la gestion des références d’objet circulaires](../../../web-api/overview/formats-and-model-binding/json-and-xml-serialization.md#handling_circular_object_references)) ; mais à l’aide d’un DTO simplement permet d’éviter le problème complètement.

Dans le modèle SPA, les DTO contient les mêmes données que les modèles de domaine. Toutefois, ils sont toujours utiles, car ils évitent les références circulaires dans les propriétés de navigation, et ils présentent le modèle DTO général.

**AccountModels.cs**

Ce fichier contient des modèles de l’appartenance au site. La `UserProfile` classe définit le schéma pour les profils utilisateur dans la base de données d’appartenance. (Dans ce cas, que les seules informations sont l’ID d’utilisateur et le nom d’utilisateur.) Les autres classes de modèle dans ce fichier sont utilisés pour créer des formulaires d’inscription et de connexion de l’utilisateur.

## <a name="entity-framework"></a>Entity Framework

Le modèle SPA utilise EF Code First. Dans le développement Code First, vous commencez par définir les modèles dans le code et EF utilise ensuite le modèle pour créer la base de données. Vous pouvez également utiliser EF avec une base de données existante ([Database First](https://msdn.microsoft.com/en-us/data/jj206878.aspx)).

Le `TodoItemContext` dérive de la classe dans le dossier de modèles **DbContext**. Cette classe fournit le type « glue » entre les modèles et EF. Le `TodoItemContext` contient un `ToDoItem` collection et un `TodoList` collection. Pour interroger la base de données, vous écrivez simplement une requête LINQ sur ces collections. Par exemple, voici comment vous pouvez sélectionner toutes les listes de tâches pour l’utilisateur « Alice » :

[!code-csharp[Main](knockoutjs-template/samples/sample2.cs)]

Vous pouvez également ajouter de nouveaux éléments à la collection, éléments, de mettre à jour ou supprimer des éléments de la collection et conserver les modifications apportées à la base de données.

## <a name="aspnet-web-api-controllers"></a>Contrôleurs de l’API Web ASP.NET

Dans ASP.NET Web API, les contrôleurs sont des objets qui gèrent les demandes HTTP. Comme indiqué, le modèle SPA utilise une API Web pour activer les opérations CRUD sur `ToDoList` et `ToDoItem` instances. Les contrôleurs sont situés dans le dossier contrôleurs, de la solution.

![](knockoutjs-template/_static/image10.png)

- `TodoController`: Gère les requêtes HTTP pour les éléments d’action
- `TodoListController`: Gère les requêtes HTTP pour les listes de tâches.

Ces noms sont importantes, car le chemin d’accès de l’URI pour le nom du contrôleur correspond à l’API Web. (Pour savoir comment API Web achemine les demandes HTTP aux contrôleurs, consultez [le routage ASP.NET Web API](../../../web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api.md).)

Examinons la `ToDoListController` classe. Il contient un membre de données unique :

[!code-csharp[Main](knockoutjs-template/samples/sample3.cs)]

Le `TodoItemContext` est utilisé pour communiquer avec EF, comme décrit précédemment. Les méthodes sur le contrôleur d’implémentent les opérations CRUD. API Web mappe les requêtes HTTP du client aux méthodes de contrôleur, comme suit :

| Requête HTTP | Méthode de contrôleur | Description |
| --- | --- | --- |
| GET /api/todo | `GetTodoLists` | Obtient une collection de listes de tâches. |
| GET/API/tâches/*id* | `GetTodoList` | Obtient une liste de tâches par ID |
| PUT/API/tâches/*id* | `PutTodoList` | Met à jour une liste de tâches. |
| POST /api/todo | `PostTodoList` | Crée une liste de tâches. |
| DELETE/API/tâches/*id* | `DeleteTodoList` | Supprime une liste de tâches. |

Notez que les URI pour certaines opérations contiennent des espaces réservés pour la valeur d’ID. Par exemple, pour supprimer une liste à avec un ID de 42, l’URI est `/api/todo/42`.

Pour en savoir plus sur l’utilisation des API Web pour les opérations, consultez [création d’une API Web qui prend en charge les opérations CRUD](../../../web-api/overview/older-versions/creating-a-web-api-that-supports-crud-operations.md). Le code pour ce contrôleur est assez simple. Voici quelques points intéressants :

- Le `GetTodoLists` méthode utilise une requête LINQ pour filtrer les résultats par l’ID de l’utilisateur connecté. De cette façon, un utilisateur ne voit que les données qui appartiennent à lui. Notez également qu’une instruction Select est utilisée pour convertir le `ToDoList` dans des instances `TodoListDto` instances.
- Les méthodes PUT et POST vérifient l’état du modèle avant de modifier la base de données. Si **ModelState.IsValid** a la valeur false, ces méthodes retournent HTTP 400 demande incorrecte. En savoir plus sur la validation de modèle dans l’API Web à [Validation du modèle](../../../web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api.md).
- La classe de contrôleur est également décorée avec le **[Authorize]** attribut. Cet attribut vérifie si la requête HTTP est authentifiée. Si la demande n’est pas authentifiée, le client reçoit HTTP 401, non autorisé. En savoir plus sur l’authentification au niveau [authentification et autorisation dans ASP.NET Web API](../../../web-api/overview/security/authentication-and-authorization-in-aspnet-web-api.md).

Le `TodoController` classe est très similaire à `TodoListController`. La plus grande différence est qu’il ne définit pas les méthodes GET, car le client obtiendra les éléments d’action, ainsi que de chaque liste de tâches.

## <a name="mvc-controllers-and-views"></a>Vues et contrôleurs MVC

Les contrôleurs MVC se trouvent également dans le dossier contrôleurs, de la solution. `HomeController`effectue le rendu HTML principal pour l’application. La vue pour le contrôleur Home est définie dans Views/Home/Index.cshtml. La vue accueil restitue un contenu différent selon que l’utilisateur est connecté :

[!code-cshtml[Main](knockoutjs-template/samples/sample4.cshtml)]

Lorsque les utilisateurs sont connectés, ils voient l’interface utilisateur principale. Dans le cas contraire, ils voient le panneau de login. Notez que cet conditionnel rendu se produit côté serveur. N’essaient jamais de masquer le contenu sensible sur le côté client & #8212anything que vous envoyez dans une réponse HTTP est visible à une personne qui surveille les messages HTTP bruts.

## <a name="client-side-javascript-and-knockoutjs"></a>Knockout.js et JavaScript côté client

Maintenant nous allons activer à partir du serveur de l’application pour le client. Le modèle SPA utilise une combinaison de jQuery et Knockout.js pour créer une interface utilisateur interactive lisse. Knockout.js est une bibliothèque JavaScript qui permet de facilement lier HTML à des données. Knockout.js utilise un modèle appelé « Model-View-ViewModel. »

- Le modèle est les données de domaine (listes de tâches et éléments de tâche).
- La vue est le document HTML.
- Le modèle d’affichage est un objet JavaScript qui contient les données de modèle. Le modèle d’affichage est une abstraction de code de l’interface utilisateur. Il n’a aucune connaissance de la représentation sous forme de code HTML. Au lieu de cela, il représente les fonctionnalités abstraites de la vue, telles que « il s’agit d’une liste des éléments de tâche ».

La vue est lié aux données pour le modèle d’affichage. Mises à jour le modèle d’affichage sont automatiquement reflétées dans la vue. Liaisons fonctionnent l’autre direction. Événements dans le DOM (tels que les clics) sont liés aux données à des fonctions sur le modèle d’affichage, qui déclenchent des appels AJAX.

Le modèle SPA organise le JavaScript côté client en trois couches :

- TODO.DataContext.js : envoie les requêtes AJAX.
- TODO.Model.js : définit les modèles.
- TODO.ViewModel.js : définit le modèle d’affichage.

![](knockoutjs-template/_static/image11.png)

Ces fichiers de script sont situés dans le dossier Scripts/application de la solution.

![](knockoutjs-template/_static/image12.png)

**TODO.DataContext** gère tous les appels AJAX pour les contrôleurs de l’API Web. (Les appels AJAX pour la connexion sont définis ailleurs, dans ajaxlogin.js.)

**TODO.Model.js** définit les modèles côté client (navigateur) pour les listes de tâches. Il existe deux classes de modèle : todoItem et liste des tâches.

La plupart des propriétés dans les classes de modèle sont de type « ko.observable ». Observables sont comment Knockout intervient. À partir de la [documentation de Knockout](http://knockoutjs.com/documentation/introduction.html): observable est un « objet JavaScript qui peut notifier les abonnés à propos des modifications. » Lorsque la valeur d’observable change, Knockout met à jour des éléments HTML qui sont liés à ces objets observable. Par exemple, todoItem a observables pour les propriétés title et quotidiennea lieu :

[!code-javascript[Main](knockoutjs-template/samples/sample5.js)]

Vous pouvez également vous abonner à observable dans le code. Par exemple, la classe todoItem s’abonne aux modifications dans les propriétés « quotidiennea lieu » et « title » :

[!code-javascript[Main](knockoutjs-template/samples/sample6.js)]

**Modèle d’affichage**

Le modèle d’affichage est défini dans todo.viewmodel.js. Le modèle d’affichage est le point central, où l’application lie les éléments de la page HTML pour les données du domaine. Dans le modèle SPA, le modèle d’affichage contient un tableau observable de todoLists. Le code suivant dans le modèle d’affichage indique Knockout pour appliquer les liaisons :

[!code-javascript[Main](knockoutjs-template/samples/sample7.js)]

## <a name="html-and-data-binding"></a>HTML et la liaison de données

Le principal HTML de la page est défini dans Views/Home/Index.cshtml. Étant donné que nous utilisons la liaison de données, le code HTML est uniquement un modèle pour ce qui en fait est restitué. Knockout utilise *déclarative* liaisons. Vous liez des éléments de page aux données en ajoutant un attribut de « lier » à l’élément. Voici un exemple très simple, tirée de la documentation de Knockout :

[!code-html[Main](knockoutjs-template/samples/sample8.html)]

Dans cet exemple, Knockout met à jour le contenu de la  **&lt;span&gt;**  élément avec la valeur de `myItems.count()`. Lorsque cette valeur change, Knockout met à jour le document.

Knockout fournit plusieurs types de liaison différents. Voici quelques-unes des liaisons utilisées dans le modèle SPA :

- **foreach**: vous permet d’itérer dans une boucle et appliquer la même balise à chaque élément dans la liste. Cela permet de restituer les listes de tâches et les éléments de tâches. Dans le **foreach**, les liaisons sont appliquées aux éléments de la liste.
- **visible**: utilisé pour afficher ou masquer. Masquer le balisage lorsqu’une collection est vide, ou afficher le message d’erreur.
- **valeur**: utilisé pour remplir les valeurs de formulaire.
- **Cliquez sur**: lie un événement click à une fonction sur le modèle d’affichage.

## <a name="anti-csrf-protection"></a>Protection de l’anti-CSRF

Falsification de requête intersites (CSRF) est une attaque où un site malveillant envoie une demande à un site vulnérable où l’utilisateur est actuellement connecté. Pour aider à empêcher les attaques par CSRF, ASP.NET MVC utilise *les jetons anti-contrefaçon*, également appelé demande de jetons de vérification. L’idée est que le serveur place un jeton généré de manière aléatoire dans une page web. Lorsque le client envoie des données au serveur, il doit inclure cette valeur dans le message de demande.

Les jetons anti-contrefaçon fonctionnent, car la page malveillante ne peut pas lire les jetons de l’utilisateur, en raison des stratégies de la même origine. (Même origine stratégies empêchent documents hébergés sur deux sites différents d’accéder au contenu de l’autre.)

ASP.NET MVC fournit la prise en charge intégrée pour les jetons anti-contrefaçon, via le [AntiForgery](https://msdn.microsoft.com/en-us/library/system.web.helpers.antiforgery.aspx) classe et le [[ValidateAntiForgeryToken]](https://msdn.microsoft.com/en-us/library/system.web.mvc.validateantiforgerytokenattribute.aspx) attribut. Actuellement, cette fonctionnalité n’est pas intégrée dans l’API Web. Toutefois, le modèle SPA inclut une implémentation personnalisée pour l’API Web. Ce code est défini dans le `ValidateHttpAntiForgeryTokenAttribute` (classe), qui se trouve dans le dossier de filtres de la solution. Pour en savoir plus sur anti-CSRF dans l’API Web, consultez [empêcher Cross-Site demande Forgery (CSRF) attaques](../../../web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks.md).

## <a name="conclusion"></a>Conclusion

Le modèle SPA est conçu pour vous aider à commencer à écrire rapidement des applications web modernes, interactif. Il utilise la bibliothèque Knockout.js pour séparer la présentation (la balise HTML) à partir de la logique de données et des applications. Mais Knockout n’est pas la bibliothèque JavaScript uniquement, que vous pouvez utiliser pour créer un SPA. Si vous souhaitez Explorer d’autres options, examinez les [créés par la Communauté des modèles SPA](../templates/index.md).
