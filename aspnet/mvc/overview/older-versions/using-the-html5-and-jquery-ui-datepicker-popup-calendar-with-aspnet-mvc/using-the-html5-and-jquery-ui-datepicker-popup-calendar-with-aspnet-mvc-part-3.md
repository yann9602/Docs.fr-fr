---
uid: mvc/overview/older-versions/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3
title: Utilisation de HTML5 et du calendrier contextuel jQuery UI avec ASP.NET MVC - partie 3 | Documents Microsoft
author: Rick-Anderson
description: "Ce didacticiel, vous allez apprendre les principes fondamentaux de l’utilisation des modèles de l’éditeur, modèles d’affichage et le calendrier contextuel jQuery UI datepicker dans un MV ASP.NET..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/29/2011
ms.topic: article
ms.assetid: 8f5f91ae-12d7-4cf3-ac09-4bb53d07ee60
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3
msc.type: authoredcontent
ms.openlocfilehash: dc81961094928025e25cf62ce4d51d12bc67b80c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc---part-3"></a>Utilisation de HTML5 et du calendrier contextuel jQuery UI avec ASP.NET MVC - partie 3
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Ce didacticiel, vous allez apprendre les principes fondamentaux de l’utilisation des modèles de l’éditeur, modèles d’affichage et le calendrier contextuel jQuery UI datepicker dans une application Web ASP.NET MVC.


## <a name="working-with-complex-types"></a>Utilisation des Types complexes

Dans cette section, vous allez créer une classe d’adresse et apprendre à créer un modèle pour l’afficher.

Dans le *modèles* dossier, créez un nouveau fichier de classe nommé *Person.cs* où vous allez placer les deux types : un `Person` classe et un `Address` classe. Le `Person` classe contiendra une propriété qui est de type `Address`. Le `Address` type est un type complexe, ce qui signifie que n’est pas un des types intégrés comme `int`, `string`, ou `double`. Au lieu de cela, elle possède plusieurs propriétés. Le code pour les nouvelles classes ressemble à ceci :

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/samples/sample1.cs)]

Dans le `Movie` contrôleur, ajoutez le code suivant `PersonDetail` action pour afficher une instance de la personne :

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/samples/sample2.cs)]

Puis ajoutez le code suivant à la `Movie` contrôleur pour remplir la `Person` modèle avec des exemples de données :

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/samples/sample3.cs)]

Ouvrez le *Views\Movies\PersonDetail.cshtml* et ajoutez le balisage suivant pour le `PersonDetail` vue.

[!code-cshtml[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/samples/sample4.cshtml)]

Appuyez sur Ctrl + F5 pour exécuter l’application et accédez à *films/PersonDetail*.

Le `PersonDetail` vue ne contient pas le `Address` type complexe, comme vous pouvez le voir dans cette capture d’écran. (Aucune adresse n’est indiqué).

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/_static/image1.png)

Le `Address` des données de modèle ne sont pas affichées, car il s’agit d’un type complexe. Pour afficher les informations d’adresse, ouvrez le *Views\Movies\PersonDetail.cshtml* à nouveau et ajoutez le balisage suivant.

[!code-cshtml[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/samples/sample5.cshtml)]

Le balisage complet pour le `PersonDetail` maintenant vue ressemble à ceci :

[!code-cshtml[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/samples/sample6.cshtml)]

Exécuter à nouveau l’application et afficher le `PersonDetail` vue. Les informations d’adresse sont maintenant affichées :

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/_static/image2.png)

### <a name="creating-a-template-for-a-complex-type"></a>Création d’un modèle pour un Type complexe

Dans cette section, vous allez créer un modèle qui sera utilisé pour restituer le `Address` type complexe. Lorsque vous créez un modèle pour le `Address` type, ASP.NET MVC pouvez automatiquement l’utiliser pour mettre en forme un modèle d’adresse n’importe où dans l’application. Cela vous permet de contrôler le rendu de la `Address` type dans un seul endroit dans l’application.

Dans le *Views\Shared\DisplayTemplates* dossier, créer une vue partielle fortement typée nommée **adresse**:

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/_static/image3.png)

Cliquez sur **ajouter**, puis ouvrez le nouveau *Views\Shared\DisplayTemplates\Address.cshtml* fichier. La nouvelle vue contient le balisage généré suivant :

[!code-cshtml[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/samples/sample7.cshtml)]

Exécutez l’application et afficher le `PersonDetail` vue. Cette fois-ci, le `Address` modèle que vous venez de créer est utilisé pour afficher le `Address` type complexe, afin que l’affichage ressemble à ce qui suit :

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/_static/image4.png)

### <a name="summary-ways-to-specify-the-model-display-format-and-template"></a>Résumé : Comment spécifier le Format d’affichage modèle et le modèle

Vous avez déjà vu que vous pouvez spécifier le format ou le modèle pour une propriété de modèle à l’aide de l’une des approches suivantes :

- Appliquer le `DisplayFormat` d’attribut à une propriété dans le modèle. Par exemple, le code suivant provoque la date à afficher sans le temps :

    [!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/samples/sample8.cs)]
- Appliquer un [type de données](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.datatype.aspx) d’attribut à une propriété dans le modèle et en spécifiant le type de données. Par exemple, le code suivant provoque la date à afficher sans le temps.

    [!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-3/samples/sample9.cs)]

    Si l’application contient un *date.cshtml* modèle dans le *Views\Shared\DisplayTemplates* dossier ou le *Views\Movies\DisplayTemplates* dossier, ce modèle permet de restituer le `DateTime` propriété. Dans le cas contraire, le système de création de modèles ASP.NET intégré affiche la propriété en tant que date.
- Création d’un modèle d’affichage dans le *Views\Shared\DisplayTemplates* dossier ou le *Views\Movies\DisplayTemplates* dossier dont le nom correspond au type de données que vous souhaitez mettre en forme. Par exemple, vous avez vu le *Views\Shared\DisplayTemplates\DateTime.cshtml* a été utilisé pour restituer `DateTime` propriétés dans un modèle, sans ajout d’un attribut au modèle et sans ajouter tout balisage aux vues.
- À l’aide de la [UIHint](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.uihintattribute.uihint.aspx) attribut sur le modèle pour spécifier le modèle pour afficher la propriété de modèle.
- Ajouter explicitement le nom de modèle d’affichage pour le [Html.DisplayFor](https://msdn.microsoft.com/en-us/library/ee407420.aspx) appeler dans une vue.

L’approche que vous utilisez dépend de ce que vous devez faire dans votre application. Il n’est pas rare de combiner ces approches pour obtenir exactement le type de mise en forme dont vous avez besoin.

Dans la section suivante, vous basculerez engins un peu et la déplacer à partir de la personnalisation de l’affichage des données pour personnaliser la façon dont il est entré. Vous pouvez raccorder le sélecteur de dates jQuery pour les vues de modification dans l’application afin de fournir un moyen efficace de spécifier des dates.

>[!div class="step-by-step"]
[Précédent](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2.md)
[Suivant](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-4.md)
