---
title: "Migration à partir de ASP.NET MVC à cœur d’ASP.NET MVC"
author: ardalis
description: 
keywords: ASP.NET Core,MVC,migration
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: 3155cc9e-d0c9-424b-886c-35c0ec6f9f4e
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/mvc
ms.openlocfilehash: 385ab7dfea5b92687a427bdfe9558462227113b1
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="migrating-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migration à partir de ASP.NET MVC à cœur d’ASP.NET MVC

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Michel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), et [Scott Addie](https://scottaddie.com)

Cet article explique comment commencer la migration d’un projet ASP.NET MVC à [ASP.NET Core MVC](../mvc/overview.md). Dans le processus, il présente la plupart des éléments qui ont été modifiés dans ASP.NET MVC. Migration à partir d’ASP.NET MVC est un processus en plusieurs étapes, et cet article couvre la configuration initiale, contrôleurs de base et les vues, contenu statique et dépendances du côté client. Autres articles couvrent la migration de la configuration et le code d’identité trouvée dans de nombreux projets ASP.NET MVC.

> [!NOTE]
> Les numéros de version dans les exemples ne sont peut-être pas en cours. Vous devrez peut-être mettre à jour vos projets en conséquence.

## <a name="create-the-starter-aspnet-mvc-project"></a>Créer le projet ASP.NET MVC starter

Pour illustrer la mise à niveau, nous allons commencer par la création d’une application ASP.NET MVC. Créer avec le nom *WebApp1* pour le projet ASP.NET Core nous créer à l’étape suivante correspond à l’espace de noms.

![Boîte de dialogue Visual Studio nouveau projet](mvc/_static/new-project.png)

![Boîte de dialogue nouvelle Application Web : modèle de projet MVC sélectionné dans le panneau de configuration de modèles ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Facultatif :* modifier le nom de la Solution à partir de *WebApp1* à *Mvc5*. Visual Studio affiche le nouveau nom de solution (*Mvc5*), ce qui rend plus facile indiquer ce projet à partir du projet suivant.

## <a name="create-the-aspnet-core-project"></a>Créer le projet ASP.NET Core

Créer un nouveau *vide* application de web ASP.NET Core avec le même nom que le projet précédent (*WebApp1*) afin de correspondre à des espaces de noms dans les deux projets. Ayant le même espace de noms rend plus facile de copier le code entre les deux projets. Vous devrez créer ce projet dans un répertoire autre que le projet précédent pour utiliser le même nom.

![Boîte de dialogue Nouveau projet](mvc/_static/new_core.png)

![Boîte de dialogue nouvelle Application Web ASP.NET : modèle de projet vide sélectionnée dans le panneau de configuration de modèles de base ASP.NET](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Facultatif :* créer une nouvelle application ASP.NET Core à l’aide du *Application Web* modèle de projet. Nommez le projet *WebApp1*, puis sélectionnez une option d’authentification de **comptes d’utilisateur individuels**. Renommer cette application *FullAspNetCore*. Création de ce projet seront gagner du temps lors de la conversion. Vous pouvez examiner le code généré de modèle pour afficher le résultat final ou copiez le code dans le projet de conversion. Il est également utile lorsque vous êtes bloqué sur une étape de conversion à comparer avec le projet de modèle généré.

## <a name="configure-the-site-to-use-mvc"></a>Configurer le site pour utiliser MVC

* Installer le `Microsoft.AspNetCore.Mvc` et `Microsoft.AspNetCore.StaticFiles` les packages NuGet.

  `Microsoft.AspNetCore.Mvc`est de l’infrastructure ASP.NET MVC de base. `Microsoft.AspNetCore.StaticFiles`est le Gestionnaire de fichiers statiques. Le runtime ASP.NET est modulaire, et vous devez explicitement s’abonner à des fichiers statiques (consultez [utilisation des fichiers statiques](../fundamentals/static-files.md)).

* Ouvrez le *.csproj* fichier (avec le bouton droit dans le projet de **l’Explorateur de solutions** et sélectionnez **WebApp1.csproj de modifier**) et ajoutez un `PrepareForPublish` cible :

  [!code-xml[Main](mvc/sample/WebApp1.csproj?range=21-23)]

  Le `PrepareForPublish` cible est nécessaire pour l’acquisition des bibliothèques côté client via Bower. Nous parlerons que plus tard.

* Ouvrez le *Startup.cs* de fichiers et de modifier le code pour faire correspondre les éléments suivants :

  [!code-csharp[Main](mvc/sample/Startup.cs?highlight=14,27-34)]

  Le `UseStaticFiles` méthode d’extension ajoute le Gestionnaire de fichiers statiques. Comme mentionné précédemment, le runtime ASP.NET est modulaire, et vous devez explicitement s’abonner à des fichiers statiques. Le `UseMvc` méthode d’extension ajoute le routage. Pour plus d’informations, consultez [démarrage de l’Application](../fundamentals/startup.md) et [routage](../fundamentals/routing.md).

## <a name="add-a-controller-and-view"></a>Ajouter un contrôleur et une vue

Dans cette section, vous allez ajouter un contrôleur minimale et la vue comme des espaces réservés pour le contrôleur ASP.NET MVC et les vues que vous allez migrer dans la section suivante.

* Ajouter un *contrôleurs* dossier.

* Ajouter un **classe de contrôleur MVC** portant le nom *HomeController.cs* à la *contrôleurs* dossier.

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/add_mvc_ctl.png)

* Ajouter un *vues* dossier.

* Ajouter un *Views/Home* dossier.

* Ajouter un *Index.cshtml* page de vue MVC à le *Views/Home* dossier.

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/view.png)

La structure de projet est indiquée ci-dessous :

![Explorateur de solutions affichant les fichiers et dossiers de WebApp1](mvc/_static/project-structure-controller-view.png)

Remplacez le contenu de la *Views/Home/Index.cshtml* fichier avec les éléments suivants :

```html
<h1>Hello world!</h1>
```

Exécutez l’application.

![Application Web ouverte dans Microsoft Edge](mvc/_static/hello-world.png)

Consultez [contrôleurs](../mvc/controllers/index.md) et [vues](../mvc/views/index.md) pour plus d’informations.

Maintenant que nous avons un projet ASP.NET Core minimale, nous pouvons commencer la migration des fonctionnalités à partir du projet ASP.NET MVC. Vous devrez déplacer les éléments suivants :

* contenu du côté client (CSS, des polices et des scripts)

* contrôleurs

* vues

* modèles

* Regroupement

* filtres

* Ouvrez une session en entrée/sortie, identité (cela est effectuée dans l’étape suivante du didacticiel.)

## <a name="controllers-and-views"></a>Contrôleurs et vues

* Chacune des méthodes copier à partir de l’ASP.NET MVC `HomeController` au nouveau `HomeController`. Notez que dans ASP.NET MVC, type de retour de méthode de modèle prédéfini contrôleur action est [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); dans ASP.NET MVC de base, les méthodes d’action retournés `IActionResult` à la place. `ActionResult`implémente `IActionResult`, il est donc inutile de modifier le type de retour de vos méthodes d’action.

* Copie le *About.cshtml*, *Contact.cshtml*, et *Index.cshtml* Razor afficher les fichiers à partir du projet ASP.NET MVC au projet ASP.NET Core.

* Exécuter l’application ASP.NET Core et chaque méthode de test. Nous n’avons pas encore effectué la migration l’ou les styles de mise en page, pour les vues de rendu ne contient que le contenu dans les fichiers de vue. Vous n’avez pas les liens de fichier généré de disposition pour le `About` et `Contact` consulte, afin d’avoir à les appeler à partir du navigateur (remplacez **4492** avec le numéro de port utilisé dans votre projet).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Page de contact](mvc/_static/contact-page.png)

Notez l’absence de style et éléments de menu. Qui sera résolu dans la section suivante.

## <a name="static-content"></a>Contenu statique

Dans les versions précédentes d’ASP.NET MVC, contenu statique a été hébergé à partir de la racine du projet web et a été inséré avec fichiers côté serveur. Dans ASP.NET Core, contenu statique est hébergé dans le *wwwroot* dossier. Que vous souhaitez copier le contenu statique à partir de votre application ASP.NET MVC ancien le *wwwroot* dossier dans votre projet ASP.NET Core. Dans cette conversion exemple :

* Copie le *favicon.ico* fichier à partir de l’ancien projet MVC dans le *wwwroot* dossier dans le projet ASP.NET Core.

L’ancien ASP.NET MVC project utilise [Bootstrap](http://getbootstrap.com/) pour que son style et le programme d’amorçage des fichiers dans le *contenu* et *Scripts* dossiers. Le modèle, ce qui a généré l’ancien projet ASP.NET MVC, fait référence à l’amorçage dans le fichier de disposition (*Views/Shared/_Layout.cshtml*). Vous pouvez copier le *bootstrap.js* et *bootstrap.css* à partir de l’ASP.NET MVC, les fichiers de projet pour le *wwwroot* n’utilise pas de dossier dans le nouveau projet, mais cette approche le mécanisme améliorée pour la gestion des dépendances de côté client dans ASP.NET Core.

Dans le nouveau projet, nous allons ajouter la prise en charge des données d’amorçage (et d’autres bibliothèques côté client) à l’aide de [Bower](https://bower.io/):

* Ajouter un [Bower](https://bower.io/) fichier de configuration nommé *bower.json* à la racine du projet (avec le bouton droit sur le projet, puis **Ajouter > nouvel élément > fichier de Configuration Bower**). Ajouter [Bootstrap](http://getbootstrap.com/) et [jQuery](https://jquery.com/) au fichier (voir les lignes en surbrillance ci-dessous).

  [!code-json[Main](mvc/sample/bower.json?highlight=5-6)]

Lors de l’enregistrement du fichier, Bower télécharge automatiquement les dépendances à la *wwwroot/lib* dossier. Vous pouvez utiliser la **recherche l’Explorateur de solutions** pour rechercher le chemin d’accès des actifs :

![ressources de jQuery apparaissent dans les résultats de recherche de l’Explorateur de solutions](mvc/_static/search.png)

Consultez [gérer les Packages côté Client avec Bower](../client-side/bower.md) pour plus d’informations.

<a name=migrate-layout-file></a>

## <a name="migrate-the-layout-file"></a>Migrer le fichier de disposition

* Copie le *_ViewStart.cshtml* fichier à partir de l’ancien projet ASP.NET MVC *vues* dossier dans le projet de ASP.NET Core *vues* dossier. Le *_ViewStart.cshtml* fichier n’a pas changé dans ASP.NET MVC de base.

* Créer un *Views/Shared* dossier.

* *Facultatif :* copie *_ViewImports.cshtml* à partir de la *FullAspNetCore* du projet MVC *vues* dossier du projet de ASP.NET Core *Vues* dossier. Supprimez toute déclaration d’espace de noms dans le *_ViewImports.cshtml* fichier. Le *_ViewImports.cshtml* fournit des espaces de noms pour tous les fichiers de l’affichage de fichiers et permet de bénéficier de [programmes d’assistance de balise](../mvc/views/tag-helpers/index.md). Programmes d’assistance de balise sont utilisés dans le nouveau fichier de disposition. Le *_ViewImports.cshtml* fichier est une nouveauté pour ASP.NET Core.

* Copie le *_Layout.cshtml* fichier à partir de l’ancien projet ASP.NET MVC *Views/Shared* dossier dans le projet de ASP.NET Core *Views/Shared* dossier.

Ouvrez *_Layout.cshtml* et apportez les modifications suivantes (le code complet est indiqué ci-dessous) :

   * Remplacez `@Styles.Render("~/Content/css")` avec un `<link>` élément à charger *bootstrap.css* (voir ci-dessous).

   * Supprimez `@Scripts.Render("~/bundles/modernizr")`.

   * Commentez la `@Html.Partial("_LoginPartial")` ligne (entourer la ligne de `@*...*@`). Nous allons y revenir dans un didacticiel futures.

   * Remplacez `@Scripts.Render("~/bundles/jquery")` avec un `<script>` élément (voir ci-dessous).

   * Remplacez `@Scripts.Render("~/bundles/bootstrap")` avec un `<script>` élément (voir ci-dessous)...

Le lien CSS de remplacement :

```html
<link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
```

Les balises de script de remplacement :

```html
<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
```

La mise à jour *_Layout.cshtml* fichier est présenté ci-dessous :

[!code-html[Main](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7,27,39-40)]

Afficher le site dans le navigateur. Il doit maintenant charger correctement, avec les styles attendus en place.

* *Facultatif :* vous souhaiterez peut-être essayer d’utiliser le nouveau fichier de disposition. Pour ce projet, vous pouvez copier le fichier de disposition à partir de la *FullAspNetCore* projet. Le nouveau fichier de mise en page utilise [programmes d’assistance de balise](../mvc/views/tag-helpers/index.md) et a d’autres améliorations.

## <a name="configure-bundling--minification"></a>Configurer le regroupement des & réduction

Pour plus d’informations sur la configuration de groupement et la minimisation, consultez [Bundling and Minification](../client-side/bundling-and-minification.md).

## <a name="solving-http-500-errors"></a>Résolution des erreurs HTTP 500

Il existe de nombreux problèmes qui peuvent provoquer un message d’erreur HTTP 500 qui ne contiennent aucune information sur la source du problème. Par exemple, si le *Views/_ViewImports.cshtml* fichier contient un espace de noms qui n’existe pas dans votre projet, vous obtiendrez une erreur HTTP 500. Pour obtenir un message d’erreur détaillé, ajoutez le code suivant :

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    if (env.IsDevelopment())
    {
         app.UseDeveloperExceptionPage();
    }

    app.UseStaticFiles();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

Consultez **à l’aide de la Page d’Exception Developer** dans [gestion des erreurs](../fundamentals/error-handling.md) pour plus d’informations.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Développement côté client](../client-side/index.md)

* [Tag Helpers](../mvc/views/tag-helpers/index.md)
