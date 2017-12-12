---
uid: mvc/overview/older-versions/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1
title: Utilisation de HTML5 et du calendrier contextuel jQuery UI avec ASP.NET MVC - partie 1 | Documents Microsoft
author: Rick-Anderson
description: "Ce didacticiel, vous allez apprendre les principes fondamentaux de l’utilisation des modèles de l’éditeur, modèles d’affichage et le calendrier contextuel jQuery UI datepicker dans un MV ASP.NET..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/29/2011
ms.topic: article
ms.assetid: c23d27f7-b0cf-44f2-8445-fb69e045c674
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1
msc.type: authoredcontent
ms.openlocfilehash: 9320c8a2aadb3b3c5bd6cd90b59d8a72db384c0c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc---part-1"></a>Utilisation de HTML5 et du calendrier contextuel jQuery UI avec ASP.NET MVC - partie 1
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Ce didacticiel, vous allez apprendre les principes fondamentaux de l’utilisation des modèles de l’éditeur, modèles d’affichage et le calendrier contextuel jQuery UI datepicker dans une application Web ASP.NET MVC.


Ce didacticiel, vous allez apprendre les principes fondamentaux de l’utilisation des modèles de l’éditeur, les modèles d’affichage et jQuery [l’interface utilisateur du calendrier contextuel](http://plugins.jquery.com/project/datepicker) dans une application Web ASP.NET MVC. Pour ce didacticiel, vous pouvez utiliser Microsoft Visual Web Developer 2010 Express Service Pack 1 (&quot;Visual Web Developer&quot;), qui est une version gratuite de Microsoft Visual Studio, ou vous pouvez utiliser Visual Studio 2010 SP1 si vous l’avez déjà.

Avant de commencer, assurez-vous que vous avez installé les composants requis répertoriés ci-dessous. Vous pouvez installer tous les en cliquant sur le lien suivant : [Web Platform Installer](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack). Vous pouvez également installer individuellement les logiciels requis à l’aide des liens suivants :

- [Conditions préalables requises de Visual Studio Web Developer Express SP1](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)
- [Mettre à jour des outils ASP.NET MVC 3](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=MVC3)
- [SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)(runtime + outils prennent en charge)

Si vous utilisez Visual Studio 2010 au lieu de Visual Web Developer, installer la configuration requise en cliquant sur le lien suivant : [composants requis de Visual Studio 2010](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack).

Ce didacticiel suppose que vous avez terminé la [mise en route avec MVC 3](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) didacticiel ou que vous êtes familiarisé avec le développement d’ASP.NET MVC. Ce didacticiel commence par le projet achevé de la [mise en route avec MVC 3](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) didacticiel.

Ce didacticiel montre le code en c#. Toutefois, le [projet de démarrage](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800) et projet terminé sont également disponibles dans Visual Basic.

Un projet Visual Studio avec code source c# et Visual Basic est disponible pour accompagner cette rubrique : [télécharger](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800).

### <a name="what-youll-build"></a>Ce que vous allez générer

Vous allez ajouter des modèles (plus particulièrement, modifier et afficher les modèles) à l’application de liste de films simple qui a été créée dans le [mise en route avec MVC 3](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) didacticiel. Vous allez également ajouter un [sélecteur de dates jQuery UI](http://jqueryui.com/demos/datepicker/) calendrier contextuel pour simplifier le processus de saisie de dates. La capture d’écran suivante montre l’application modifiée avec l’interface utilisateur de jQuery du calendrier contextuel affiché.

![jQuery terminé](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image1.png)

### <a name="skills-youll-learn"></a>Vous allez apprendre des compétences

Voici ce que vous allez apprendre :

- Comment utiliser les attributs de la [DataAnnotations](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.aspx) espace de noms pour contrôler le format de données lorsqu’elle est affichée et lorsqu’il est en mode d’édition.
- Comment créer des modèles (modifier et afficher les modèles) pour contrôler la mise en forme des données.
- Comment ajouter la [sélecteur de dates jQuery UI](http://jqueryui.com/demos/datepicker/) comme un moyen d’entrer des champs de date.

### <a name="getting-started"></a>Commencer

Si vous n’avez pas encore l’application de liste de films à partir du projet de démarrage, le télécharger à l’aide du lien suivant : [télécharger](https://code.msdn.microsoft.com/Project/Download/FileDownload.aspx?https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800). Puis, dans l’Explorateur Windows, cliquez sur le *MvcMovie.zip* fichier et sélectionnez **propriétés**. Dans le **MvcMovie.zip propriétés** boîte de dialogue, sélectionnez **Unblock**. (Le déblocage empêche un avertissement de sécurité qui se produit lorsque vous essayez d’utiliser un *.zip* fichier que vous avez téléchargé à partir du web.)

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image2.png)

Avec le bouton droit le *MvcMovie.zip* fichier et sélectionnez **extraire tout** pour décompresser le fichier. Dans Visual Web Developer ou Visual Studio 2010, ouvrez le *MvcMovieCS\_TU.sln* fichier.

Dans **l’Explorateur de solutions**, double-cliquez sur le *Views\Shared\\_Layout.cshtml* pour l’ouvrir. Modifier la `H1` en-tête à partir de **application MVC est film** à **film jQuery**. Appuyez sur CTRL + F5 pour exécuter l’application et cliquez sur le **accueil** onglet, qui permet d’accéder à la `Index` méthode du contrôleur vidéo. Pour tester l’application, sélectionnez le **modifier** lien et le **détails** lien pour l’une des séquences. Notez que dans l’Index, modification, et les vues de détails, la date de publication et les prix sont correctement mis en forme :

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image3.png)

La mise en forme pour la date et le prix est le résultat de l’utilisation de la [DisplayFormat](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.displayformatattribute.aspx) attribut sur les propriétés de la `Movie` classe.

Ouvrir le *Movie.cs* de fichiers et commentez la `DisplayFormat` de l’attribut le `ReleaseDate` et `Price` propriétés. Résultant `Movie` classe ressemble à ceci :

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/samples/sample1.cs)]

Appuyez sur CTRL + F5 pour exécuter l’application et sélectionnez le **accueil** onglet pour afficher la liste de films. Cette fois, la date de publication affiche la date et l’heure, et le champ prix n’affiche plus le symbole monétaire. Vos modifications dans le `Movie` classe a annulé la mise en forme agréable que vous avez vu précédemment, mais vous allez résoudre cela dans un instant.

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image4.png)

### <a name="using-the-dataannotations-datatype-attribute-to-specify-the-data-type"></a>Pour spécifier le Type de données à l’aide de l’attribut de type de données DataAnnotations

Remplacer la commenté `DisplayFormat` d’attribut pour le `ReleaseDate` propriété avec le [DataType](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatype.aspx) d’attribut, à l’aide de la `Date` énumération. Remplacez le `DisplayFormat` d’attribut pour le `Price` propriété avec le [type de données](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatype.aspx) attribut là encore, cette fois en utilisant le `Currency` énumération. Voici à quoi ressemble le code terminé :

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/samples/sample2.cs)]

Exécutez l'application. Maintenant la date de publication et les propriétés de prix sont correctement mis en forme (qui est, à l’aide des formats de date et de devise appropriés). Le [DataType](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatype.aspx) attribut fournit des métadonnées de type pour l’intégrées ASP.NET MVC modèles afin que les champs de rendu dans le format correct. À l’aide de la `DataType` attribut est préférable à l’utilisation de la `DisplayFormat` attribut qui a été initialement dans le code, car le `DataType` attribut rend le modèle plus claire et plus souple pour des raisons comme internationalisation.

Dans la section suivante, vous verrez comment créer des modèles personnalisés pour afficher les champs de date.

>[!div class="step-by-step"]
[Next](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2.md)
