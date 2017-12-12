---
uid: web-pages/overview/ui-layouts-and-themes/creating-and-using-a-helper-in-an-aspnet-web-pages-site
title: "Création et à l’aide d’un programme d’assistance dans ASP.NET Web Pages (Razor) Site | Documents Microsoft"
author: tfitzmac
description: "Cet article décrit comment créer un programme d’assistance dans un site Web ASP.NET Web Pages (Razor). Un programme d’assistance est un composant réutilisable qui inclut le code et le balisage perf..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/17/2014
ms.topic: article
ms.assetid: 46bff772-01e0-40f0-9ae6-9e18c5442ee6
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/creating-and-using-a-helper-in-an-aspnet-web-pages-site
msc.type: authoredcontent
ms.openlocfilehash: 5d0c1ae09d8fbc91ff76cd4045d439abafee7736
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-and-using-a-helper-in-an-aspnet-web-pages-razor-site"></a>Création et utilisation d’un programme d’assistance dans un Site de Web Pages (Razor) ASP.NET
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article décrit comment créer un programme d’assistance dans un site Web ASP.NET Web Pages (Razor). A *assistance* est un composant réutilisable qui inclut le code et le balisage pour effectuer une tâche qui peut être fastidieux ou complexe.
> 
> **Ce que vous allez apprendre :** 
> 
> - Comment créer et utiliser un programme d’assistance simple.
> 
> Voici les fonctionnalités d’ASP.NET introduites dans l’article :
> 
> - Le `@helper` syntaxe.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 3
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2.


## <a name="overview-of-helpers"></a>Vue d’ensemble des applications d’assistance

Si vous avez besoin effectuer les mêmes tâches sur les différentes pages de votre site, vous pouvez utiliser un programme d’assistance. Les Pages Web ASP.NET inclut un certain nombre de programmes d’assistance et beaucoup d’autres que vous pouvez télécharger et installer. (Les programmes d’assistance intégrées dans ASP.NET Web Pages est répertoriés dans le [référence rapide de l’API ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202907).) Si aucun des programmes d’assistance existants répondent à vos besoins, vous pouvez créer votre propre programme d’assistance.

Une application d’assistance permet d’utiliser un bloc courants de code sur plusieurs pages. Vous voulez que votre page souvent créer un élément de la Remarque qui est défini indépendamment des paragraphes normaux. Par exemple la note est créée en tant qu’un `<div>` élément qui a le format d’une zone avec une bordure. Au lieu d’ajouter ce même balisage à une page chaque fois que vous souhaitez afficher une note, vous pouvez empaqueter le balisage comme une application d’assistance. Vous pouvez ensuite insérer la Remarque avec une seule ligne de code n’importe où vous en avez besoin.

À l’aide d’un programme d’assistance ainsi de rend le code dans chacune de vos pages plus simple et plus facile à lire. Il rend également plus facile à maintenir votre site, car si vous devez modifier l’apparence des notes, vous pouvez modifier le balisage dans un seul emplacement.

## <a name="creating-a-helper"></a>Création d’une application d’assistance

Cette procédure vous montre comment créer l’application d’assistance qui crée une note, comme décrit ci-dessus. Il s’agit d’un exemple simple, mais l’application d’assistance personnalisée peut inclure un balisage et le code ASP.NET dont vous avez besoin.

1. Dans le dossier racine du site Web, créez un dossier nommé *application\_Code*. Il s’agit d’un nom de dossier réservé dans ASP.NET où vous pouvez placer le code des composants tels que des programmes d’assistance.
2. Dans le *application\_Code* dossier créer un nouveau *.cshtml* de fichier et nommez-la *MyHelpers.cshtml*.
3. Remplacez le contenu existant avec les éléments suivants :

    [!code-cshtml[Main](creating-and-using-a-helper-in-an-aspnet-web-pages-site/samples/sample1.cshtml)]

    Le code utilise le `@helper` syntaxe pour déclarer une nouvelle assistance de nommé `MakeNote`. Ce programme d’assistance particulier vous permet de passer un paramètre nommé `content` qui peut contenir une combinaison de texte et de balises. L’application d’assistance insère la chaîne dans le corps de la Remarque à l’aide du `@content` variable.

    Notez que le fichier est nommé *MyHelpers.cshtml*, mais l’application d’assistance est nommée `MakeNote`. Vous pouvez placer plusieurs applications d’assistance personnalisées dans un seul fichier.
4. Enregistrez et fermez le fichier.

## <a name="using-the-helper-in-a-page"></a>À l’aide de l’application d’assistance dans une Page

1. Dans le dossier racine, créez un fichier vide appelé *TestHelper.cshtml*.
2. Ajoutez le code suivant au fichier :

    [!code-html[Main](creating-and-using-a-helper-in-an-aspnet-web-pages-site/samples/sample2.html)]

    Pour appeler l’application d’assistance que vous avez créé, utilisez `@` suivi par le nom du fichier où l’application d’assistance est, un point, puis le nom de l’application d’assistance. (Si vous avez plusieurs dossiers dans le *application\_Code* dossier, vous pouvez utiliser la syntaxe `@FolderName.FileName.HelperName` pour appeler votre application d’assistance dans n’importe quelle imbriquées au niveau du dossier). Le texte que vous ajoutez des guillemets entre parenthèses est le texte qui affiche l’application d’assistance dans le cadre de la note dans la page web.
3. Enregistrez la page et l’exécuter dans un navigateur. L’application d’assistance génère l’élément note à droite dans lequel vous avez appelé l’application d’assistance : entre les deux paragraphes.

    ![Capture d’écran affichant la page dans le navigateur et comment l’application d’assistance généré le balisage qui place une zone autour du texte spécifié.](creating-and-using-a-helper-in-an-aspnet-web-pages-site/_static/image1.jpg)

## <a name="additional-resources"></a>Ressources supplémentaires


[Menu horizontal sous la forme d’un programme d’assistance Razor](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2341). Cette entrée de blog par Mike Pope montre comment créer un menu horizontal comme un programme d’assistance à l’aide du balisage, CSS et le code.

[Tirant parti de HTML5 dans ASP.NET Web Pages des programmes d’assistance pour WebMatrix et ASP.NET MVC3](http://geekswithblogs.net/wildturtle/archive/2010/11/08/html5-in-asp.net-web-pages-helpers-for-webmatrix-and_aspnet_mvc3.aspx). Cette entrée de blog par Sam Abraham montre un programme d’assistance qui restitue une HTML5 `Canvas` élément.

[La différence entre @Helpers et @Functions dans WebMatrix](http://www.mikesdotnetting.com/Article/173/The-Difference-Between-@Helpers-and-@Functions-In-WebMatrix). Décrit de cette entrée de blog par Mike Brind `@helper` syntaxe et `@function` syntaxe et quand les utiliser.
