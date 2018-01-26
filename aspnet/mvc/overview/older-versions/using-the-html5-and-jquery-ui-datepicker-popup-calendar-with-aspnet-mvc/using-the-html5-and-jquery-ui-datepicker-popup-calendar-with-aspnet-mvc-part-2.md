---
uid: mvc/overview/older-versions/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2
title: Utilisation de HTML5 et du calendrier contextuel jQuery UI avec ASP.NET MVC - partie 2 | Documents Microsoft
author: Rick-Anderson
description: "Ce didacticiel, vous allez apprendre les principes fondamentaux de l’utilisation des modèles de l’éditeur, modèles d’affichage et le calendrier contextuel jQuery UI datepicker dans un MV ASP.NET..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/29/2011
ms.topic: article
ms.assetid: 21a178de-4c5a-4211-8a9c-74ec576c0f30
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2
msc.type: authoredcontent
ms.openlocfilehash: 69fbaa7761c97895ffee770f6feb9ce6b745d186
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc---part-2"></a>Utilisation de HTML5 et du calendrier contextuel jQuery UI avec ASP.NET MVC - partie 2
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Ce didacticiel, vous allez apprendre les principes fondamentaux de l’utilisation des modèles de l’éditeur, modèles d’affichage et le calendrier contextuel jQuery UI datepicker dans une application Web ASP.NET MVC.


## <a name="adding-an-automatic-datetime-template"></a>Ajout d’un modèle de date/heure automatique

Dans la première partie de ce didacticiel, vous avez vu comment vous pouvez ajouter des attributs au modèle pour spécifier explicitement la mise en forme, et comment vous pouvez spécifier explicitement le modèle utilisé pour restituer le modèle. Par exemple, le [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) attribut dans l’explicitement de code suivant spécifie que la mise en forme pour le `ReleaseDate` propriété.

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/samples/sample1.cs)]

Dans l’exemple suivant, la [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) d’attribut, à l’aide de la `Date` énumération, spécifie que le modèle de date doit être utilisé pour restituer le modèle. S’il n’existe aucun modèle de date dans votre projet, le modèle de date intégré est utilisé.

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/samples/sample2.cs)]

Toutefois, ASP. MVC peut effectuer une correspondance de type à l’aide de la convention-over-configuration, en recherchant un modèle qui correspond au nom d’un type. Cela vous permet de créer un modèle qui met automatiquement les données sans utiliser de tout attribut ou le code du tout. Pour cette partie du didacticiel, vous allez créer un modèle qui est automatiquement appliqué aux propriétés de modèle de type [DateTime](https://msdn.microsoft.com/library/system.datetime.aspx). Vous n’aurez pas à utiliser un attribut ou autre élément de configuration pour spécifier que le modèle doit être utilisé pour afficher toutes les propriétés du modèle de type [DateTime](https://msdn.microsoft.com/library/system.datetime.aspx).

Vous découvrirez également la possibilité de personnaliser l’affichage des propriétés individuelles ou même des champs spécifiques.

Pour commencer, nous allons supprimer les informations de mise en forme existantes et afficher des dates complètes dans l’application.

Ouvrir le *Movie.cs* de fichiers et commentez la `DataType` de l’attribut le `ReleaseDate` propriété :

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/samples/sample3.cs)]

Appuyez sur CTRL+F5 pour exécuter l'application.

Notez que le `ReleaseDate` propriété affiche maintenant la date et l’heure, car il s’agit de la valeur par défaut lorsque aucune information de mise en forme est fournie.

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/_static/image1.png)

### <a name="adding-css-styles-for-testing-new-templates"></a>Ajout de Styles CSS pour tester de nouveaux modèles

Avant de créer un modèle de mise en forme des dates, vous allez ajouter quelques règles de style CSS que vous pouvez appliquer aux nouveaux modèles. Ils vous aideront à vérifier que la page rendue utilise le nouveau modèle.

Ouvrez le *Content\Site.cs*s de fichier et ajouter les règles CSS suivants en bas du fichier :

[!code-css[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/samples/sample4.css)]

### <a name="adding-datetime-display-templates"></a>Ajout de modèles d’affichage de date/heure

Vous pouvez maintenant créer le nouveau modèle. Dans le *Views\Movies* dossier, créez un *DisplayTemplates* dossier.

Dans le *Views\Shared* dossier, créez un *DisplayTemplates* dossier et un *EditorTemplates* dossier.

Les modèles d’affichage dans le *Views\Shared\DisplayTemplates* dossier sera utilisé par tous les contrôleurs. Les modèles d’affichage dans le *Views\Movie\DisplayTemplates* dossier sera utilisé uniquement par la `Movie` contrôleur. (Si un modèle portant le même nom apparaît dans les deux dossiers, le modèle dans le *Views\Movie\DisplayTemplates* dossier : autrement dit, le modèle plus spécifique, ont la priorité sur les vues retournées par la `Movie` contrôleur.)

Dans **l’Explorateur de solutions**, développez le *vues* dossier, développez le *Shared* dossier et avec le bouton droit puis le *Views\Shared\DisplayTemplates* dossier.

Cliquez sur **ajouter** puis cliquez sur **vue**. Le **ajouter une vue** boîte de dialogue s’affiche.

Dans le **nom de la vue** , tapez `DateTime`. (Vous devez utiliser ce nom pour faire correspondre le nom du type).

Sélectionnez le **créer comme une vue partielle** case à cocher. Assurez-vous que le **utiliser une disposition ou la page maître** et **créer une vue fortement typée** cases à cocher ne sont pas sélectionnés.

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/_static/image2.png)

Cliquez sur **Ajouter**. A *DateTime.cshtml* modèle est créé dans le *Views\Shared\DisplayTemplates*.

L’illustration suivante montre le *vues* dossier dans **l’Explorateur de solutions** après le `DateTime` de création de modèles d’affichage et l’éditeur.

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/_static/image3.png)

Ouvrez le *Views\Shared\DisplayTemplates\DateTime.cshtml* et ajoutez le balisage suivant, qui utilise le [String.Format](https://msdn.microsoft.com/library/system.string.format.aspx) méthode pour mettre en forme la propriété comme une date sans heure. (Le `{0:d}` format Spécifie le format de date courte.)

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/samples/sample5.cs)]

Répétez cette étape pour créer un `DateTime` modèle dans le *Views\Movie\DisplayTemplates* dossier. Utilisez le code suivant dans le *Views\Movie\DisplayTemplates\DateTime.cshtml* fichier.

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/samples/sample6.cs)]

La `loud-1` classe CSS provoque la date être affiché en texte gras rouge. Vous avez ajouté la `loud-1` classe CSS comme une mesure temporaire afin de voir facilement que ce modèle particulier est utilisé.

Ce que vous avez fait est créé et les modèles ASP.NET utilisera pour afficher des dates personnalisés. Le modèle plus général (dans le *Views\Shared\DisplayTemplates* dossier) affiche une date courte simple. Le modèle qui est spécifiquement conçu pour le `Movie` contrôleur (dans le *Views\Movies\DisplayTemplates* dossier) affiche une date courte qui est également mis en forme en tant que texte gras rouge.

Appuyez sur CTRL+F5 pour exécuter l'application. Le navigateur restitue la vue Index pour l’application.

Le `ReleaseDate` propriété affiche maintenant la date en gras rouge sans le temps. Cela vous permet de confirmer que le `DateTime` programme d’assistance basé sur un modèle dans le *Views\Movies\DisplayTemplates* dossier est sélectionné sur la `DateTime` programme d’assistance basé sur un modèle dans le dossier partagé (*Views\Shared\ DisplayTemplates*).

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/_static/image4.png)

Renommez maintenant la *Views\Movies\DisplayTemplates\DateTime.cshtml* le fichier *Views\Movies\DisplayTemplates\LoudDateTime.cshtml*.

Appuyez sur CTRL+F5 pour exécuter l'application.

Cette fois le `ReleaseDate` propriété affiche une date sans heure et sans la police rouge en gras. Cela montre que le type de modèle qui porte le nom des données (dans ce cas `DateTime`) est automatiquement utilisé pour afficher toutes les propriétés de modèle de ce type. Après avoir renommé le *DateTime.cshtml* le fichier *LoudDateTime.cshtml*, ASP.NET trouvé n’est plus un modèle dans le *Views\Movies\DisplayTemplates* dossier, il peut donc utilisé le *DateTime.cshtml* modèle à partir de la * Views\Movies\Shared\* dossier.

(La correspondance de modèle étant la casse, vous pourriez avoir créés le nom de fichier de modèle avec n’importe quel boîtier. Par exemple, *DATETIME.chstml, datetime.cshtml*, et *DaTeTiMe.cshtml* correspondrait à tous les `DateTime` type.)

Pour passer en revue : à ce stade, le `ReleaseDate` de champ est affiché à l’aide de la *Views\Movies\DisplayTemplates\DateTime.cshtml* modèle, qui affiche les données à l’aide d’un format de date courte, mais sinon n’ajoute aucun format spécial.

### <a name="using-uihint-to-specify-a-display-template"></a>À l’aide de la propriété UIHint pour spécifier un modèle d’affichage

Si votre application web possède de nombreuses `DateTime` champs par défaut que vous souhaitez afficher la totalité ou la plupart d'entre elles dans un format de date uniquement, le *DateTime.cshtml* modèle est une bonne approche. Mais que se passe-t-il si vous avez plusieurs dates où vous souhaitez afficher la date et heure complètes ? Aucun problème. Vous pouvez créer un modèle supplémentaire et utiliser le [UIHint](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.uihintattribute.uihint.aspx) attribut pour spécifier la mise en forme pour la date et heure complètes. Vous pouvez ensuite appliquer de manière sélective ce modèle. Vous pouvez utiliser la [UIHint](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.uihintattribute.uihint.aspx) attribut au niveau du modèle, ou vous pouvez spécifier le modèle à l’intérieur d’une vue. Dans cette section, vous allez apprendre à utiliser le `UIHint` attribut à modifier de manière sélective la mise en forme pour certaines instances de champs de date et d’heure.

Ouvrez le *Views\Movies\DisplayTemplates\LoudDateTime.cshtml* de fichier et remplacez le code existant par le code suivant :

[!code-cshtml[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/samples/sample7.cshtml)]

Cela provoque la date et heure complètes à afficher et ajoute la classe CSS qui rend le texte vert et volumineux.

Ouvrez le *Movie.cs* et ajoutez le [UIHint](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.uihintattribute.uihint.aspx) attribut le `ReleaseDate` propriété, comme indiqué dans l’exemple suivant :

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/samples/sample8.cs)]

Cela indique à ASP.NET MVC que lorsqu’il affiche la `ReleaseDate` propriété (en particulier et pas seulement les `DateTime` objet), elle doit utiliser le *LoudDateTime.cshtml* modèle.

Appuyez sur CTRL+F5 pour exécuter l'application.

Notez que le `ReleaseDate` propriété affiche maintenant la date et l’heure dans une grande taille de police vert.

Retour à la `UIHint` d’attribut dans le *Movie.cs* de fichiers et commentez-le afin que la *LoudDateTime.cshtml* modèle ne seront pas utilisé. Exécutez de nouveau l'application. La date de publication ne s’affiche pas volumineux et vert. Il vérifie que le *Views\Shared\DisplayTemplates\DateTime.cshtml* modèle est utilisée dans les vues d’Index et les détails.

Comme mentionné précédemment, vous pouvez également appliquer un modèle dans une vue, ce qui vous permet d’appliquer le modèle à une instance individuelle de certaines données. Ouvrez le *Views\Movies\Details.cshtml* vue. Ajouter `"LoudDateTime"` comme deuxième paramètre de la [Html.DisplayFor](https://msdn.microsoft.com/library/ee407420.aspx) appeler pour le `ReleaseDate` champ. Le code complet ressemble à ceci :

[!code-cshtml[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/samples/sample9.cshtml)]

Cela indique que le `LoudDateTime` modèle doit être utilisé pour afficher la propriété de modèle, indépendamment de quels attributs sont appliqués au modèle.

Appuyez sur CTRL+F5 pour exécuter l'application.

Vérifiez que la page d’index de films utilise le *Views\Shared\DisplayTemplates\DateTime.cshtml* modèle (rouge en gras) et le *Movie\Details* à l’aide de la page le *Views\Movies\ DisplayTemplates\LoudDateTime.cshtml* modèle (volumineux et vert).

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2/_static/image5.png)

Dans la section suivante, vous allez créer un modèle pour un type complexe.

>[!div class="step-by-step"]
[Précédent](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1.md)
[Suivant](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3.md)
