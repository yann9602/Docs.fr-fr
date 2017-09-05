---
title: "À l’aide de Bower dans ASP.NET Core"
author: rick-anderson
description: "Gestion des packages Bower côté client."
keywords: ASP.NET Core, bower
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: df7c43da-280e-4df6-86cb-eecec8f12bfc
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/bower
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 409f7afba8dd7d03b7b9aa27d93ec9167252b965
ms.sourcegitcommit: 4e84d8bf5f404bb77f3d41665cf7e7374fc39142
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2017
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a>Gérer les packages côté client avec Bower dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel riz](http://blog.falafel.com/author/noel-rice/), et [Scott Addie](https://scottaddie.com) 

[Bower](https://bower.io/) s’appelle elle-même « Gestionnaire de package pour le web. » Dans l’écosystème .NET, il remplit le vide laissé par l’impossibilité de NuGet pour remettre les fichiers de contenu statique. Pour les projets ASP.NET Core, ces fichiers statiques sont inhérents aux bibliothèques côté client comme [jQuery](http://jquery.com/) et [Bootstrap](http://getbootstrap.com/). Pour les bibliothèques .NET, vous utilisez toujours [NuGet](https://nuget.org/) Gestionnaire de package.

Processus de génération de nouveaux projets créés avec les modèles de projet ASP.NET Core configuration côté client. [jQuery](http://jquery.com/) et [Bootstrap](http://getbootstrap.com/) sont installés, et Bower est pris en charge.

Les packages côté client sont répertoriés dans le *bower.json* fichier. Les modèles de projet ASP.NET Core configure *bower.json* avec jQuery, jQuery validation et d’amorçage.

Dans ce didacticiel, nous allons ajouter la prise en charge de [police impressionnant](http://fontawesome.io). Bower packages peuvent être installés avec le **gérer les Packages Bower** UI ou manuellement dans le *bower.json* fichier.

### <a name="installation-via-manage-bower-packages-ui"></a>Installation via gérer les Packages Bower l’interface utilisateur

* Créer une application ASP.NET Core Web avec le **Application ASP.NET Core Web (.NET Core)** modèle. Sélectionnez **Application Web** et **aucune authentification**.

* Cliquez sur le projet dans l’Explorateur de solutions et sélectionnez **gérer les Packages Bower** (également dans le menu principal, **projet** > **gérer les Packages Bower**).

* Dans le **Bower : \<nom du projet\>**  fenêtre, cliquez sur l’onglet « Parcourir », puis filtrer la liste de packages en entrant `font-awesome` dans la zone de recherche :

 ![gérer les packages bower](bower/_static/manage-bower-packages.png)

* Vérifiez que le « enregistrer les modifications *bower.json*« case à cocher est activée. Sélectionnez une version dans la liste déroulante et cliquez sur le **installer** bouton. Le **sortie** fenêtre affiche les détails d’installation.

### <a name="manual-installation-in-bowerjson"></a>Installation manuelle de bower.json

Ouvrez le *bower.json* et ajoutez « police impressionnant » aux dépendances. IntelliSense affiche les packages disponibles. Lorsqu’un package est sélectionné, les versions disponibles sont affichées. Les images ci-dessous sont plus anciennes et ne correspondra pas à ce que vous voyez.

![IntelliSense de l’Explorateur de package bower](bower/_static/add-package.png)

![version de bower IntelliSense](bower/_static/version-intelliSense.png)

Bower utilise [contrôle de version sémantique](http://semver.org/) pour organiser les dépendances. Contrôle de version sémantique, également appelé SemVer, identifie les packages avec le modèle de numérotation \<majeure >.\< mineure >. \<correctif >. IntelliSense simplifie le contrôle de version sémantique en affichant uniquement quelques choix courants. Le premier élément dans la liste IntelliSense (4.6.3 dans l’exemple ci-dessus) est considéré comme la dernière version stable du package. Le symbole du signe insertion (^) correspond à la version majeure la plus récente et le tilde (~) correspond à la version mineure la plus récente.

Enregistrer le *bower.json* fichier. Visual Studio surveille le *bower.json* des modifications. Lors de l’enregistrement, la *bower installation* commande est exécutée. Consultez la fenêtre de sortie **Bower/npm** vue pour la commande exacte exécutée.

Ouvrez le *.bowerrc* de fichiers sous *bower.json*. Le `directory` est définie sur *wwwroot/lib* qui indique l’emplacement Bower installera les composants de package.

```json
{
 "directory": "wwwroot/lib"
}
```

Vous pouvez utiliser la zone de recherche dans l’Explorateur de solutions pour rechercher et afficher le package impressionnant de police.

Ouvrez le *Views\Shared\_Layout.cshtml* et ajoutez le fichier CSS impressionnant de police à l’environnement [application d’assistance de balise](xref:mvc/views/tag-helpers/intro) pour `Development`. À partir de l’Explorateur de solutions, faites glisser et déposez *awesome.css de police* à l’intérieur du `<environment names="Development">` élément.

[!code-html[Main](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

Dans une application de production, vous ajouteriez *awesome.min.css de police* dans l’application d’assistance de balise environnement pour `Staging,Production`.

Remplacez le contenu de la *Views\Home\About.cshtml* fichier Razor avec le balisage suivant :

[!code-html[Main](bower/sample/About.cshtml)]

Exécutez l’application et accédez à la vue à propos de pour vérifier le fonctionnement du package impressionnant de police.

## <a name="exploring-the-client-side-build-process"></a>Explorer le processus de génération du côté client

La plupart des modèles de projet ASP.NET Core sont déjà configurés pour utiliser Bower. Cette procédure pas à pas suivante commence par un projet ASP.NET Core vide et ajoute chaque élément manuellement, vous pouvez obtenir une idée pour l’utilisation de Bower dans un projet. Vous voyez peuvent devenir de la structure de projet et de l’exécution sous forme de chaque modification de la configuration est effectuée.

Les étapes générales à utiliser le processus de génération de côté client avec Bower sont :

* Définir les packages utilisés dans votre projet. <!-- once defined, you don't need to download them, VS does -->
* Référence à des packages à partir de vos pages web.

### <a name="define-packages"></a>Définir des packages

Une fois que la liste de packages dans le *bower.json* fichier, Visual Studio permet de le télécharger. L’exemple suivant utilise Bower pour charger jQuery et amorçage pour le *wwwroot* dossier.

* Créer une application ASP.NET Core Web avec le **Application ASP.NET Core Web (.NET Core)** modèle. Sélectionnez le **vide** modèle de projet et cliquez sur **OK**.

* Dans l’Explorateur de solutions, cliquez sur le projet > **ajouter un nouvel élément** et sélectionnez **fichier de Configuration Bower**. Remarque : Un *.bowerrc* fichier est également ajouté.

* Ouvrez *bower.json*et ajouter de jquery et données d’amorçage pour le `dependencies` section. Résultant *bower.json* fichier ressemblera à l’exemple suivant. Les versions changent au fil du temps et ne peuvent pas correspondre à l’image ci-dessous.

[!code-json[Main](bower/sample/bower.json?highlight=5,6)]

* Enregistrer le *bower.json* fichier.

 Vérifiez que le projet inclut le *amorçage* et *jQuery* répertoires dans *wwwroot/lib*. Bower utilise le *.bowerrc* fichier pour installer les composants dans *wwwroot/lib*.

 Remarque : L’interface utilisateur « Gérer les Packages Bower » offre une alternative à la modification du fichier manuelle.

### <a name="enable-static-files"></a>Activer les fichiers statiques

* Ajouter le `Microsoft.AspNetCore.StaticFiles` package NuGet pour le projet.
* Activer des fichiers statiques avec la [intergiciel (middleware) du fichier statique](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.staticfileextensions). Ajoutez un appel à [UseStaticFiles](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.staticfileextensions) à la `Configure` méthode `Startup`.

[!code-csharp[Main](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a>Packages de référence

Dans cette section, vous allez créer une page HTML pour vérifier qu’il peut accéder aux packages déployés.

* Ajouter une nouvelle page HTML nommée *Index.html* à la *wwwroot* dossier. Remarque : Vous devez ajouter le fichier HTML pour le *wwwroot* dossier. Par défaut, le contenu statique ne peut pas être traitée en dehors de *wwwroot*. Consultez [utilisation des fichiers statiques](xref:fundamentals/static-files) pour plus d’informations.

 Remplacez le contenu de *Index.html* par le balisage suivant :

[!code-html[Main](bower/sample/Index.html)]

* Exécutez l’application et accédez à `http://localhost:<port>/Index.html`. Vous pouvez également avec *Index.html* ouvert, appuyez sur `Ctrl+Shift+W`. Vérifiez que le style jumbotron est appliqué, le code jQuery répond quand le bouton est activé, et que le bouton d’amorçage modifie l’état.

 ![style de JumboTron appliqué](bower/_static/jumbotron.png)
